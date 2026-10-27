sequenceDiagram
    actor User as Користувач
    participant Client as Frontend
    participant API as ProgramController
    participant Service as ProgramService
    participant DB as DataBase

    User->>Client: Натискає "Опублікувати"
    activate Client

    Client->>API: PATCH /programs/{id}/publish
    activate API

    API->>Service: publishProgram(programId, userId)
    activate Service
    
    Service->>DB: getUserData(userId)
    activate DB
    DB-->>Service: return userData 
    deactivate DB
    
  
    alt userData.role.includes("Trainer") 

        Service->>DB: update(programId, 'Public')
        activate DB
        DB-->>Service: success
        deactivate DB
        
        Service-->>API: updatedProgram
        API-->>Client: HTTP 200 OK
        Client-->>User: "Програму опубліковано!"
    else !userData.roles.includes("Trainer")
        Service-->>API: throw ForbiddenException("Функція доступна лише тренерам")
        API-->>Client: HTTP 403 Forbidden
        Client-->>User: Помилка: "Функція доступна лише тренерам"

    end
    
    deactivate Service
    deactivate API
    deactivate Client