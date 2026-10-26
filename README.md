name: Gerar APK Liberty IA

on:
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Baixar projeto
        uses: actions/checkout@v4

      - name: Extrair projeto
        run: |
          mkdir projeto
          unzip -q LibertyIA_Projeto_com_Compilacao_APK.zip -d projeto
          find projeto -name gradlew -type f -exec chmod +x {} \;

      - name: Configurar Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Compilar APK
        working-directory: projeto
        run: |
          ./gradlew assembleDebug --no-daemon

      - name: Publicar APK
        uses: softprops/action-gh-release@v2
        with:
          tag_name: apk-latest
          name: Liberty IA APK
          files: projeto/**/build/outputs/apk/debug/*.apk
          make_latest: true
