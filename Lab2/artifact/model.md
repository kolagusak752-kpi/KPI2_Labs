flowchart LR
    User((Користувач))
    Athlete((Атлет))
    Trainer((Тренер))
  

    Athlete --> User
    Trainer --> User

    UC_Role([Отримати роль])
    UC_Pay([Оплатити підписку])
    UC_CreateProg([Створити тренувальну програму])
    UC_DeleteProg([Видалити програму])
    UC_ChooseProg([Обрати програму])
    
    UC_PublishProg([Опублікувати програму])
    UC_ArchiveProg([Архівувати програму])
    
    UC_Workout([Провести тренування])
    UC_ViewProg([Переглядати програму])
    UC_History([Переглядати історію тренувань])
    UC_Progress([Відслідковувати прогрес])
    UC_FindTrainer([Знайти тренера])
    
    UC_SaveInc([Зберегти незавершене тренування])
    
    UC_Transaction([Обробка транзакції])

    User --- UC_Role
    User --- UC_Pay
    User --- UC_CreateProg
    User --- UC_DeleteProg

    Trainer --- UC_PublishProg

    Athlete --- UC_Workout
    Athlete --- UC_ViewProg
    Athlete --- UC_History
    Athlete --- UC_Progress
    Athlete --- UC_FindTrainer
    Athlete --- UC_ChooseProg

    UC_Pay --- UC_Transaction

    UC_SaveInc -.->|"<< extend >>"| UC_Workout
    UC_PublishProg -.->|"<< extend >>"| UC_CreateProg
    UC_ArchiveProg -.->|"<< extend >>"| UC_DeleteProg
