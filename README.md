# QA Automation inteverview - Transfer API

## Transfer API requirements

The Transfers API implements the following requirements:

  - **QA-API-001** The system must allow one user to transfer funds to another.
  - **QA-API-002** The system must ensure atomicity, guaranteeing a transfer is only successful if the fund can be deducted from user A and added to user B
  - **QA-API-003** The system must allow an overdraft amount per user.
  - **QA-API-004** The system must allow that when a user gets without funds, he can still transfer partially or all from their overdraft account
  - **QA-API-005** The system must ensure that users can only use partially or a total of their overdraft account once a day
  - **QA-API-006** The system must ensure users are not allowed to transfer amounts greater than their available funds + overdraft amount (if they have not already used part of that during the day)
  - **QA-API-007** The system must ensure money transferred to user, first covers what is owned from the overdraft account and then their personal funds
  - **QA-API-008** The system must ensure that even if a user receives enough transfers to cover their user overdraft debt, they cannot use it again on the same day

## API details

These are some details of the API.

- It consumes and publishes application/json
- There are a set of built-in users that are reset everytime the application starts. They are:


| User ID  | Funds     | Overdraft limit |
| -------- | --------- | --------------- |
| 111111   | 1000      | 250             |
| 222222   | 300       | 100             |
| 333333   | 100       | 0               |
| 444444   | 250       | 300             |
| 555555   | 575       | 600             |
| 666666   | 200       | 500             |
| 777777   | 1000      | 0               |

- There's a Get API to be able to view the details of the users. The API is published under /api/users/{user_id}
```shell
$> curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://HOST/api/users/111111
 HTTP/1.0 200 OK
Content-Type: application/json
Content-Length: 67
Server: Werkzeug/1.0.1 Python/3.6.9
Date: Thu, 22 Oct 2020 23:55:08 GMT

{"funds":1000,"id":111111,"overdraft_debt":0,"overdraft_limit":250}
```

- There's an API for testers to be able to create new users. it is a POST API published unders /api/users:
```shell
$> curl -H "Accept: application/json" -H "Content-Type: application/json" -d @data.json http://HOST/api/users

{"funds":5000,"id":874347,"overdraft_debt":0,"overdraft_limit":125}

$> cat data.json
{
        "funds": 5000,
        "overdraft_limit": 600
}
```
- There's an API to make transfers. It is a POST API published under /api/transfers:
```shell
$> curl -H "Accept: application/json" -H "Content-Type: application/json" -X POST -d @transfer.json http://HOST/api/transfers

{"funds":4650,"id":898259,"overdraft_debt":0,"overdraft_limit":125}

$> cat transfer.json

{
        "from": 898259,
        "to": 874347,
        "amount": 350
}
```
