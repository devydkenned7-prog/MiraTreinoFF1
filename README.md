name: Gerar APK

on:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Baixar projeto
        uses: actions/checkout@v4

      - name: Configurar Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Extrair projeto
        run: |
          mkdir projeto
          unzip -q MiraTreinoFF_Pro_v2.zip -d projeto

      - name: Encontrar pasta do projeto Android
        run: |
          echo "Procurando settings.gradle..."
          find projeto -name "settings.gradle" -o -name "settings.gradle.kts"

      - name: Gerar APK
        run: |
          PASTA=$(dirname "$(find projeto -name "settings.gradle" -o -name "settings.gradle.kts" | head -n 1)")
          echo "Projeto encontrado em: $PASTA"
          cd "$PASTA"
          gradle assembleDebug

      - name: Salvar APK
        uses: actions/upload-artifact@v4
        with:
          name: MiraTreinoFF-APK
          path: projeto/**/build/outputs/apk/**/*.apk
