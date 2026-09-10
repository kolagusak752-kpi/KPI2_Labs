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
    int exercise_order 
    int reps
    int sets

6) Program_Item:
    uuid id PK
    uuid program_id FK
    uuid workout_id FK
    int workout_order

Зв'язки:
    Кожен відвідувач може мати лише одну програму тренувань у конкретний момент часу , бо програма орієнтована на одну активність , а саме тренажерний зал, одну програму може використовувати декілька відвідувачів

    Кожна програма містить декілька тренувань та одне тренування може перевикористовуватися у декількох програмах

    Одне тренування містить декілька вправ та одна вправа може міститися у декількох програмах

