# Описание сущностей базы данных Game Records Management System

## 1. **Users**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **username**: varchar(50) (NOT NULL, UNIQUE)
- **email**: varchar(255) (NOT NULL, UNIQUE)
- **password_hash**: varchar(255) (NOT NULL)
- **created_at**: timestamp (NOT NULL)
- **is_active**: boolean (NOT NULL, DEFAULT true)

**OtO**:
- **UserProfiles**;
- **UserStats**;

**OtM**:
- **Records**;
- **Comments**;
- **AuditLogs**;

**MtM**:
- **Roles**;
- **Users**.

Сущность пользователя системы, содержащая основную информацию для авторизации и идентификации.

## 2. **Roles**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **name**: varchar(50) (NOT NULL, UNIQUE)
- **description**: text

**MtM**:
- **Users**.

Сущность ролей системы (admin, moderator, player).

## 3. **UserRoles**
- **user_id**: serial (NOT NULL, FK -> Users(id))
- **role_id**: serial (NOT NULL, FK -> Roles(id))
- **assigned_at**: timestamp (NOT NULL)
- **assigned_by**: serial (NOT NULL, FK -> Users(id))

**MtM**:
- **Users**;
- **Roles**.

Промежуточная сущность для связи Many-to-Many между пользователями и ролями.

## 4. **UserProfiles**
- **user_id**: serial (NOT NULL, FK -> Users(id), UNIQUE)
- **display_name**: varchar(100)
- **country**: varchar(100)
- **timezone**: varchar(50)
- **bio**: text
- **avatar_url**: text
- **social_links**: jsonb

**OtO**:
- **Users**.

Сущность профиля пользователя с дополнительной информацией.

## 5. **Games**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **title**: varchar(255) (NOT NULL)
- **description**: text
- **release_year**: integer
- **developer**: varchar(255)
- **publisher**: varchar(255)
- **cover_image_url**: text
- **is_active**: boolean (NOT NULL, DEFAULT true)

**OtM**:
- **Categories**;
- **Records**.

Сущность игр, доступных в системе.

## 6. **Platforms**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **name**: varchar(100) (NOT NULL, UNIQUE)
- **manufacturer**: varchar(100)
- **release_year**: integer

**OtM**:
- **Records**.

Сущность игровых платформ.

## 7. **Categories**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **name**: varchar(255) (NOT NULL)
- **description**: text
- **rules**: text (NOT NULL)
- **game_id**: serial (NOT NULL, FK -> Games(id))

**MtO**:
- **Games**.

**OtM**:
- **Records**.

Сущность категорий скоростного прохождения для каждой игры.

## 8. **SubmissionStatuses**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **name**: varchar(50) (NOT NULL, UNIQUE)
- **description**: text

**OtM**:
- **Records**;
- **RecordSubmissions**.

Сущность статусов заявок на рекорды.

## 9. **Records**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **completion_time**: interval (NOT NULL)
- **achieved_at**: timestamp (NOT NULL)
- **submission_date**: timestamp (NOT NULL)
- **verification_date**: timestamp
- **player_id**: serial (NOT NULL, FK -> Users(id))
- **game_id**: serial (NOT NULL, FK -> Games(id))
- **category_id**: serial (NOT NULL, FK -> Categories(id))
- **platform_id**: serial (NOT NULL, FK -> Platforms(id))
- **verified_by**: serial (FK -> Users(id))
- **status_id**: serial (NOT NULL, FK -> SubmissionStatuses(id))

**MtO**:
- **Users**;
- **Games**;
- **Categories**;
- **Platforms**;
- **SubmissionStatuses**.

**OtO**:
- **RecordSubmissions**;

**OtM**:
- **Evidence**;
- **Comments**.

Сущность игровых рекордов.

## 10. **RecordSubmissions**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **record_id**: serial (NOT NULL, FK -> Records(id), UNIQUE)
- **submission_notes**: text
- **review_notes**: text
- **review_date**: timestamp
- **reviewer_id**: serial (FK -> Users(id))
- **status_id**: serial (NOT NULL, FK -> SubmissionStatuses(id))

**MtO**:
- **Records**;
- **Users**;
- **SubmissionStatuses**.

Сущность заявок на верификацию рекордов.

## 11. **Evidence**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **record_id**: serial (NOT NULL, FK -> Records(id))
- **evidence_type**: varchar(50) (NOT NULL)
- **url**: text (NOT NULL)
- **file_size**: bigint
- **upload_date**: timestamp (NOT NULL)
- **description**: text

**MtO**:
- **Records**.

Сущность доказательств рекордов.

## 12. **Comments**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **content**: text (NOT NULL)
- **created_at**: timestamp (NOT NULL)
- **author_id**: serial (NOT NULL, FK -> Users(id))
- **record_id**: serial (NOT NULL, FK -> Records(id))
- **parent_comment_id**: serial (FK -> Comments(id))

**MtO**:
- **Users**;
- **Records**;
- **Comments**.

Сущность комментариев к рекордам.

## 13. **UserStats**
- **user_id**: serial (NOT NULL, FK -> Users(id), UNIQUE)
- **total_records**: integer (NOT NULL, DEFAULT 0)
- **verified_records**: integer (NOT NULL, DEFAULT 0)
- **total_play_time**: interval
- **favorite_game_id**: serial (FK -> Games(id))
- **rank_position**: integer

**OtO**:
- **Users**.

**MtO**:
- **Games**.

Сущность статистики игроков.

## 14. **Friendships**
- **user_id**: serial (NOT NULL, FK -> Users(id))
- **friend_id**: serial (NOT NULL, FK -> Users(id))
- **friendship_date**: timestamp (NOT NULL)
- **status**: varchar(20) (NOT NULL)

**MtM**:
- **Users**.

Промежуточная сущность для связи Many-to-Many между пользователями (система друзей).

## 15. **AuditLogs**
- **id**: serial (PK, NOT NULL, UNIQUE)
- **action**: varchar(100) (NOT NULL)
- **description**: text
- **performed_at**: timestamp (NOT NULL)
- **moderator_id**: serial (NOT NULL, FK -> Users(id))
- **target_user_id**: serial (FK -> Users(id))
- **target_record_id**: serial (FK -> Records(id))
- **ip_address**: inet

**MtO**:
- **Users**;
- **Users**;
- **Records**.

Сущность логов модерации.