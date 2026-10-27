sequenceDiagram
    actor User 
    participant Client as Frontend
    participant API as ProgramController
    participant Service as ProgramService
    participant DB as DataBase

    User->>Client: Натискає "Зберегти програму"
    activate Client

    Client->>API: POST /programs { workouts, settings }
    activate API

    API->>Service: createProgram(workouts, settings)
    activate Service

    Service->>DB: insertProgram(settings)
    activate DB
    DB-->>Service: return program
    deactivate DB

    Service->>DB: insertWorkouts(program.id, workouts)
    activate DB
    DB-->>Service: return workouts
    deactivate DB

    loop for each workout
        Service->>DB: insertExercises(workout.id, exercises)
        activate DB
        DB-->>Service: return exercises
        deactivate DB

        loop for each exercise
            Service->>DB: insertSets(exercise.id, sets)
            activate DB
            DB-->>Service: return sets
            deactivate DB
        end
    end
    
    Service-->>API: return program
    deactivate Service
    
    API-->>Client: HTTP 201 Created
    deactivate API
    
    Client-->>User: "Програму успішно створено"
    deactivate Client