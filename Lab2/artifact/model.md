@startuml
left to right direction
skinparam packageStyle rectangle

actor "Користувач" as User
actor "Атлет" as Athlete
actor "Тренер" as Trainer

Athlete -u-|> User
Trainer -u-|> User

package "Тренувальний застосунок" {
    usecase "Отримати роль" as UC_Role
    usecase "Оплатити підписку" as UC_Pay
    usecase "Створити тренувальну програму" as UC_CreateProg
    usecase "Видалити програму" as UC_DeleteProg
    usecase "Обрати програму" as UC_ChooseProg
    
    usecase "Опублікувати програму" as UC_PublishProg
    usecase "Архівувати програму" as UC_ArchiveProg
    
    usecase "Провести тренування" as UC_Workout
    usecase "Переглядати програму" as UC_ViewProg
    usecase "Переглядати історію тренувань" as UC_History
    usecase "Відслідковувати прогрес" as UC_Progress
    usecase "Знайти тренера" as UC_FindTrainer
    
    usecase "Зберегти незавершене\nтренування" as UC_SaveInc
    usecase "Обробка транзакції" as UC_Transaction
}


User -- UC_Role
User -- UC_Pay
User -- UC_CreateProg
User -- UC_DeleteProg

Trainer -- UC_PublishProg

Athlete -- UC_ChooseProg
Athlete -- UC_Workout
Athlete -- UC_ViewProg
Athlete -- UC_History
Athlete -- UC_Progress
Athlete -- UC_FindTrainer

UC_Pay -- UC_Transaction

UC_SaveInc .> UC_Workout : <<extend>>
UC_PublishProg .> UC_CreateProg : <<extend>>
UC_ArchiveProg .> UC_DeleteProg : <<extend>>

@enduml
