name: Manual CI

on:
  workflow_dispatch:

env:
  SERVER_IP: ${{ secrets.SERVER_IP }}
  SERVER_USER: ${{ secrets.SERVER_USER }}
  SERVER_PATH: ${{ secrets.SERVER_PATH }}

jobs:
  backend:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: backend

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup .NET 8
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.0.x'

      - name: Restore
        run: dotnet restore Menufy.sln

      - name: Build
        run: dotnet build Menufy.sln -c Release --no-restore

      - name: Show server config (debug)
        run: |
          echo "Server IP: $SERVER_IP"
          echo "User: $SERVER_USER"
          echo "Path: $SERVER_PATH"

  frontend:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: frontend

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: 'frontend/package-lock.json'

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npm run build
