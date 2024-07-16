# coinchaud
## Features
- Users can create and share lists of places to visit.
- Users can sign up with social auth / login / logout.
- Users can CRUD lists.
- Users can add a specific Point of Interest (POI) to the list.
- Users can add a comment/more info to a given POI.
- Users can share this list with anyone.

## Web Service Endpoints

### Authentication
1. **POST /auth/signup**
   - **Description**: Sign up a new user.
   - **Request Body**:
     ```json
     { "email": "string", "password": "string", "name": "string" }
     ```
   - **Response**:
     ```json
     { "message": "User created successfully", "user_id": "uuid" }
     ```

2. **POST /auth/login**
   - **Description**: Log in an existing user.
   - **Request Body**:
     ```json
     { "email": "string", "password": "string" }
     ```
   - **Response**:
     ```json
     { "token": "jwt_token" }
     ```

3. **POST /auth/social**
   - **Description**: Social login.
   - **Request Body**:
     ```json
     { "provider": "string", "token": "string" }
     ```
   - **Response**:
     ```json
     { "token": "jwt_token" }
     ```

4. **POST /auth/logout**
   - **Description**: Log out the current user.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Response**:
     ```json
     { "message": "Logged out successfully" }
     ```

### User Profile
1. **GET /user/profile**
   - **Description**: Get the profile of the logged-in user.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Response**:
     ```json
     { "user": { "id": "uuid", "name": "string", "email": "string" } }
     ```

2. **PUT /user/profile**
   - **Description**: Update the profile of the logged-in user.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Request Body**:
     ```json
     { "name": "string", "email": "string" }
     ```
   - **Response**:
     ```json
     { "message": "Profile updated successfully" }
     ```

### Lists and POIs
1. **POST /lists**
   - **Description**: Create a new list.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Request Body**:
     ```json
     { "name": "string", "description": "string" }
     ```
   - **Response**:
     ```json
     { "list_id": "uuid", "message": "List created successfully" }
     ```

2. **GET /lists**
   - **Description**: Get all lists of the logged-in user.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Response**:
     ```json
     { "lists": [ { "id": "uuid", "name": "string", "description": "string" } ] }
     ```

3. **GET /lists/{list_id}**
   - **Description**: Get a specific list by ID.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Response**:
     ```json
     { "list": { "id": "uuid", "name": "string", "description": "string", "pois": [ ... ] } }
     ```

4. **PUT /lists/{list_id}**
   - **Description**: Update a specific list.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Request Body**:
     ```json
     { "name": "string", "description": "string" }
     ```
   - **Response**:
     ```json
     { "message": "List updated successfully" }
     ```

5. **DELETE /lists/{list_id}**
   - **Description**: Delete a specific list.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Response**:
     ```json
     { "message": "List deleted successfully" }
     ```

6. **POST /lists/{list_id}/pois**
   - **Description**: Add a POI to a list.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Request Body**:
     ```json
     { "poi_id": "uuid", "comment": "string" }
     ```
   - **Response**:
     ```json
     { "message": "POI added to list successfully" }
     ```

7. **DELETE /lists/{list_id}/pois/{poi_id}**
   - **Description**: Remove a POI from a list.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Response**:
     ```json
     { "message": "POI removed from list successfully" }
     ```

8. **POST /pois**
   - **Description**: Create a new POI.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Request Body**:
     ```json
     { "name": "string", "description": "string", "location": { "latitude": "float", "longitude": "float" } }
     ```
   - **Response**:
     ```json
     { "poi_id": "uuid", "message": "POI created successfully" }
     ```

9. **GET /pois/{poi_id}**
   - **Description**: Get details of a specific POI.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Response**:
     ```json
     { "poi": { "id": "uuid", "name": "string", "description": "string", "location": { "latitude": "float", "longitude": "float" } } }
     ```

### Sharing
1. **POST /lists/{list_id}/share**
   - **Description**: Share a list with another user.
   - **Headers**: `Authorization: Bearer jwt_token`
   - **Request Body**:
     ```json
     { "email": "string" }
     ```
   - **Response**:
     ```json
     { "message": "List shared successfully" }
     ```

## UML Database Structure

### Tables
1. **UserTable**
   - `id: UUID` (Primary Key)
   - `name: String`
   - `email: String`
   - `password: String`
   - `created_at: DateTime`
   - `updated_at: DateTime`

2. **SocialProfileTable**
   - `id: UUID` (Primary Key)
   - `user_id: UUID` (Foreign Key to UserTable)
   - `provider: String`
   - `social_id: String`
   - `created_at: DateTime`
   - `updated_at: DateTime`

3. **SpotListTable**
   - `id: UUID` (Primary Key)
   - `user_id: UUID` (Foreign Key to UserTable)
   - `name: String`
   - `description: String`
   - `created_at: DateTime`
   - `updated_at: DateTime`

4. **SpotTable**
   - `id: UUID` (Primary Key)
   - `name: String`
   - `description: String`
   - `latitude: Float`
   - `longitude: Float`
   - `created_at: DateTime`
   - `updated_at: DateTime`

5. **CombinedTables**
   - `id: UUID` (Primary Key)
   - `spotlist_id: UUID` (Foreign Key to SpotListTable)
   - `spot_id: UUID` (Foreign Key to SpotTable)
   - `comment: String`
   - `created_at: DateTime`
   - `updated_at: DateTime`

### Diagram
```plaintext
+-------------------+     +-------------------+     +-------------------+
|    UserTable      |     | SocialProfileTable|     |   SpotListTable   |
+-------------------+     +-------------------+     +-------------------+
| id (PK)           |<--->| user_id (FK)      |<--->| user_id (FK)      |
| name              |     | provider          |     | name              |
| email             |     | social_id         |     | description       |
| password          |     | created_at        |     | created_at        |
| created_at        |     | updated_at        |     | updated_at        |
| updated_at        |     +-------------------+     +-------------------+
+-------------------+                              
                                                
                                                
+-------------------+     +-------------------+     
|     SpotTable     |     |  CombinedTables   |     
+-------------------+     +-------------------+     
| id (PK)           |<--->| spot_id (FK)      |     
| name              |     | spotlist_id (FK)  |     
| description       |     | comment           |     
| latitude          |     | created_at        |     
| longitude         |     | updated_at        |     
| created_at        |     +-------------------+     
| updated_at        |                                
+-------------------+                                
```
