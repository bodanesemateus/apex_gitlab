# Versionamento Apex com GitLab

Versionamento do Oracle Apex no GIT ou GIT LAB.

Para este versionamento iremos utilizar a seguinte stack:

- Docker
- SQLcl
- Visual Studio Code
- LiquidBase
- Git

O processo de versionamento acava sendo muito simples, o processo de CI/CD acaba entrando neste mesmo "pacote" de simplicidade.

# Exportacao dos dados

Para exportar os dados do Apex, existem N's formatos, o melhor dele em um cenario de versionamento via GIT contendo CI/CD acaba sendo via SQLcl.

Para isto, iremos subir um container no Docker utilizando SQLcl:

```
docker run --rm -it -v ./:/opt/oracle/sql_scripts container-registry.oracle.com/database/sqlcl:latest USER/PASSWORD@DNS:1521/SERVICE_NAME
```

Este comando acima, ira criar um container com ultima versao do SQLcl, conectado no banco de dados setado.

O comando acima, mapeia para o container a pasta no qual esta sendo executado o comando, ou seja, caso voce esteja na pasta _/home/mateus-bodanese/testes/_ ele ira exportar os dados dentro da pasta TESTES, pois foi criado um volume com o ``` -v ./```.

Feito isto, podemos executar o comando ``` apex help``` para obter comandos que podem ser executado.

```
SQL> apex help

  Usage: 
  apex|ax COMMAND {OPTIONS}
  apex|ax  help|he [-example|-ex]
  apex|ax  help|he COMMAND [-syntax|-sy] [-example|-ex]

  The following commands are available within the apex feature.

  Commands:
    export|ex
    Export APEX applications and or workspaces. 

    export-all-applications|exaa
    Export all applications in a workspace

    export-all-workspaces|exaw
    Export all workspaces without there applications, structure only.

    export-application|exap
    Export all or the parts of an application

    export-components|exco
    Export specific components within an application

    export-feedback|exfe
    Export workspace feedback 

    export-instance|exin
    Export all applications in all workspaces that are not hidden

    export-static-files|exsf
    Export static files in a workspace

    export-workspace|exwo
    Export workspace without the applications, structure only.

    list|li
    Generate a list of available components
    
```

Para saber qual applicacao iremos exportar, vamos executar o comando ``` apex list ```

```
SQL> apex list

WORKSPACE_ID        WORKSPACE    APPLICATION_ID    APPLICATION_NAME    BUILD_STATUS       LAST_UPDATED_ON  
2044838282373994    AVCF         110               Aves Campo          Run and Develop    08-DEC-23      
```
Ou seja, para exportar iremos usar os seguintes comandos:
```
apex export -applicationid 110 -workspaceid 2044838282373994 -split -skipExportDate -expOriginalIds -expSupportingObjects Y -expType APPLICATION_SOURCE,READABLE_YAML,EMBEDDED_CODE
```

Apos exportar, na pasta onde criamos o volume teremos o seguinte resultado:
![image info](./export.png)


Para realizar o versionamento no Git, eu particularmente uso o VSCode, pela simplicidade, para versionar basta eu ir ate ``` Source Control -> Selecionar os arquivos -> Adicionar uma menssagem -> Commit -> Sync os dados```
