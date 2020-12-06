# User Authentication Service

This code comes from following the video series [Building an Authentication Service using Actix](https://youtu.be/AH2P7Vc0N9s) (see [author's code](https://github.com/nemesiscodex/user-auth)).

I didn't really modify anything here, as I was just using this as a learning experience.

The big learning for me here was:
* the sqlx library is pretty cool. It's controversial to have the compiler connect to the database to infer schema, but it feels worth it to get true type safety checks on the database interactions. Having experienced one too many schema changes in the past blow up code I wasn't even thinking about at the time, this level of safety feels very helpful.
* with enough supporting crates, Actix-web feels really productive and fluent. For new projects, I don't see any reason NOT to use Rust for APIs at this point. I agree with https://www.arewewebyet.org/ !
* the error handling in Rust is just fantastic. It's so cool to see how `color_eyre::Result` can be used throughout just by tacking on the `.context(...)` to a native `Result<U, T>` type. In Go, this simple microservice would have its fair share of `if err != nil` hell.

All of the below notes are taken from the [original repo's](https://github.com/nemesiscodex/user-auth) README, with minor modifications:

### Usage

```bash
# SQLX CLI
cargo install --version=0.2.0 sqlx-cli

# Run docker-compose (postgres database)
docker-compose up

# Run migrations
sqlx mig run

# Run the server (http://localhost:3000)
cargo run
```

### API
- User Signup: `POST` /signup
  ```
  curl --request POST \
    --url http://localhost:3000/signup \
    --header 'content-type: application/json' \
    --data '{
        "username": "user1",
        "email": "user1@example.com",
        "password": "user1pass"
    }'
  ```
- Auth: `POST` /auth
  ```
  curl --request POST \
    --url http://localhost:3000/auth \
    --user user1
  ```
- User profile: `GET` /me
  ```
  curl --request GET \
  --url http://localhost:3000/me \
  --header 'authorization: Bearer <jwt_token>'
  ```
- Update profile: `PUT` /me
  ```
  curl --request POST \
    --url http://localhost:3000/me \
    --header 'authorization: Bearer <jwt_token>' \
    --header 'content-type: application/json' \
    --data '{
        "full_name": "User One"
    }'
  ```
- Example jwt token (debug it in [JWT.io](https://jwt.io/)):  
  ```
  eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhNzM1Mjk0Yi04OWNhLTQ0NzktYTRjMi05NDhmNzE3OWRkZjAiLCJleHAiOjE2MDczNjgwNzN9.UrxB3bx5kH0hQ45MuTb8AqJZWRZuJyuIXvUVPS9IjvY
  ```