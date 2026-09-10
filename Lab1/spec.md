Сутності:
1) Athlete:
    uuid id PK
    string name 
    uuid current_program_id FK

2) Program:
    uuid id PK
    string name 
    
3) Workout:
    uuid id PK
    string name
    uuid program_id

4) Exercise:
    uuid id PK
    string name
    string description

5) Workout_Item:
    uuid id PK
    uuid exercise_id FK
    uuid workout_id FK
    int reps
    int sets