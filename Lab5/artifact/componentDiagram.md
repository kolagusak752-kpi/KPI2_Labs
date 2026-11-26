@startuml
skinparam componentStyle uml2
skinparam Padding 8
skinparam DefaultTextAlignment center
skinparam shadowing false

actor "Користувач" as User

rectangle "Fitness Platform" {

  package "Frontend" {
    component "Catalog Page" as CatPage
    component "Program Builder Page" as ProgPage
    component "Workout Session Page" as SessPage
    component "Progress Page" as ProgressPage
    component "History Page" as HistPage
    component "Profile Page" as ProfPage
    component "Subscription Page" as SubPage
  }

  interface "REST /api" as IRest

  component "Fitness Backend" as BE {
    portin "HTTP in" as pHttp
    portin "webhook in" as pWh
    portout "PSP out" as pPsp
    portout "push out" as pPush
    portout "Cache out" as pCache
    portout "SQL out" as pSql

    package "Controllers" {
      component "Router" as Router
      component "AuthMiddleware" as AuthMw
      component "AuthController" as AuthCtrl
      component "ProgramController" as ProgCtrl
      component "WorkoutSessionController" as SessCtrl
      component "AnalyticsController" as AnalytCtrl
      component "SubscriptionController" as SubCtrl
      component "PaymentWebhookController" as PayWhCtrl
      component "SubscriptionCronJob" as SubCron

      Router ..> AuthMw
      Router ..> AuthCtrl
      Router ..> ProgCtrl
      Router ..> SessCtrl
      Router ..> AnalytCtrl
      Router ..> SubCtrl
      Router ..> PayWhCtrl
    }

    package "Services" {
      component "NotificationService" as NotifSvc
      component "AuthService" as AuthSvc
      component "ProgramService" as ProgSvc
      component "WorkoutSessionService" as SessSvc
      component "AnalyticsService" as AnalytSvc
      component "SubscriptionService" as SubSvc

      ProgSvc ..> NotifSvc
      SessSvc ..> SubSvc
      SubSvc ..> NotifSvc
    }

    package "Repositories" {
      component "UserRepository" as UserRepo
      component "ProgramRepository" as ProgRepo
      component "WorkoutSessionRepository" as SessRepo
      component "SubscriptionRepository" as SubRepo
    }

  
    pHttp --> Router
    pWh --> Router

    ' Controllers -> Services
    AuthMw ..> AuthSvc
    AuthCtrl ..> AuthSvc
    ProgCtrl ..> ProgSvc
    SessCtrl ..> SessSvc
    AnalytCtrl ..> AnalytSvc
    SubCtrl ..> SubSvc
    PayWhCtrl ..> SubSvc
    SubCron ..> SubSvc

    ' Services -> Repositories
    AuthSvc ..> UserRepo
    ProgSvc ..> ProgRepo
    ProgSvc ..> UserRepo
    SessSvc ..> SessRepo
    AnalytSvc ..> SessRepo
    SubSvc ..> SubRepo
    SubSvc ..> UserRepo


    NotifSvc --> pPush
    SubSvc --> pPsp
    ProgSvc --> pCache

    UserRepo --> pSql
    ProgRepo --> pSql
    SessRepo --> pSql
    SubRepo --> pSql

    Router -[hidden]down- ProgCtrl
    ProgCtrl -[hidden]down- ProgSvc
    ProgSvc -[hidden]down- ProgRepo
  }

  interface "SQL" as ISql
  interface "Cache API" as ICache

  database "Fitness DB\n(PostgreSQL)" as DB
  database "Redis Cache\n(кеш каталогу)" as Redis
}


component "Payment Processor" <<external>> as PP
component "Push Provider" <<external>> as Push

interface "Webhook" as IWh
interface "Payments API" as IPP
interface "Push API" as IPush


User --> CatPage
User --> ProgPage
User --> SessPage
User --> ProgressPage
User --> HistPage
User --> ProfPage
User --> SubPage


CatPage --( IRest
ProgPage --( IRest
SessPage --( IRest
ProgressPage --( IRest
HistPage --( IRest
ProfPage --( IRest
SubPage --( IRest
pHttp -() IRest

pSql --( ISql
DB -() ISql

pCache --( ICache
Redis -() ICache

pWh -() IWh
PP --( IWh

PP -() IPP
pPsp --( IPP

Push -() IPush
pPush --( IPush
@enduml