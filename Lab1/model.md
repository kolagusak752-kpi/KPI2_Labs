erDiagram
Program ||--o{ Athlete : ""

Program ||--o{ Program_Item : ""

Workout ||--o{ Program_Item : ""

Workout ||--o{ Workout_Item : ""

Exercise ||--o{ Workout_Item : ""

Program {
    uuid id PK
    string name
}

Athlete {
    uuid id PK
    string name
    uuid current_program_id FK

}

Workout {
    uuid id PK
    string name
}

Program_Item {
    uuid id PK
    uuid program_id FK
    uuid workout_id FK
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
    int reps
    int sets
}