erDiagram
Program ||--o{ Athlete : ""

Program ||--o{ Program_Item : ""

Workout ||--o{ Program_Item : ""

Workout ||--o{ Workout_Item : ""

Exercise ||--o{ Workout_Item : ""

Trainer o|--o{ Program : ""

Trainer o|--o{ Program_Item : ""

Subscription o{--|| Tariff : ""

Athlete ||--o| Subscription : ""



Program {
    uuid id PK
    string name
    uuid trainer_id FK
}

Athlete {
    uuid id PK
    string name
    uuid current_program_id FK
}

Trainer {
    uuid id PK
    string name
}

Workout {
    uuid id PK
    string name
}

Program_Item {
    uuid id PK
    uuid program_id FK
    uuid workout_id FK
    uuid trainer_id FK
    int workout_order
    
}
Exercise {
    uuid id PK
    string name
    string description
}
Workout_Item {
    uuid id PK
    uuid workout_id FK
    uuid exercise_id FK
    int max_reps
    int min_reps
    int sets
}
Subscription {
    uuid id PK
    uuid tariff_id FK
    Date start_date
    bool isPaid
    
}