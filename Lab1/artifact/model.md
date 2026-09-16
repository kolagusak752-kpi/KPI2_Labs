erDiagram
    User ||--o| Athlete : ""
    User ||--o| Trainer : ""
    Program o|--o{ Athlete : ""
    Trainer |o--o{ Program : ""
    Workout ||--o{ Program_Item : ""
    Program ||--o{ Program_Item : ""
    Workout ||--o{ Workout_Item : ""
    Exercise ||--o{ Workout_Item : ""
    Tariff ||--o{ Subscription : ""
    User ||--o| Subscription : ""
    Workout ||--o{ Workout_Session : ""
    Trainer |o--o{ Workout_Session : ""
    User ||--o{ Workout_Session : ""
    Workout_Session ||--o{ Workout_Session_Exercise : ""
    Exercise ||--o{ Workout_Session_Exercise : ""
    Workout_Session_Exercise ||--o{ Workout_Session_Set : ""

    Athlete {
        uuid user_id PK,FK
        string name
        uuid current_program_id FK
    }

    Program {
        uuid id PK
        string name
        uuid trainer_id FK
    }

    Workout {
        uuid id PK
        string name
    }

    Exercise {
        uuid id PK
        string name
        string description
    }

    Workout_Item {
        uuid id PK
        uuid exercise_id FK
        uuid workout_id FK
        int exercise_order
        int min_reps
        int max_reps
        int sets
    }

    Program_Item {
        uuid id PK
        uuid program_id FK
        uuid workout_id FK
        int workout_order
    }

    Trainer {
        uuid user_id PK,FK
        string name
    }

    Tariff {
        uuid id PK
        string name
        int period
        int price
    }

    Subscription {
        uuid id PK
        uuid tariff_id FK
        uuid user_id FK
        Date start_date
        bool isPaid
    }

    User {
        uuid id PK
        string email
        string phone_number
        string hashed_password
        bool isTrainer
        bool isAthlete
    }

    Workout_Session {
        uuid id PK
        uuid workout_id FK
        uuid trainer_id FK
        int price
        bool isPaid
        timestamp date
    }

    Workout_Session_Exercise {
        uuid id PK
        uuid workout_session_id FK
        uuid exercise_id FK
    }

    Workout_Session_Set {
        uuid id PK
        uuid workout_session_exercise_id FK
        int order
        float weight
        int reps
    }