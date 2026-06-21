Clone the repo, place .env and sessions.jsonl in the repo root, then run:

cp nitter.example.conf nitter.conf
docker compose up -d --build

Test:
curl -fsS http://127.0.0.1:8080/api/v1/health
curl -i http://127.0.0.1:8080/api/v1/users/jack
curl -i --get http://127.0.0.1:8080/api/v1/search/tweets --data-urlencode 'q="Owner.com" restaurant'
