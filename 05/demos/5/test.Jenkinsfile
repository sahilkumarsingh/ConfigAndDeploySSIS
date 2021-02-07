node {
    stage('SCM') {
		git changelog: false, poll: false, url: 'https://github.com/FeynmanFan/configuring-ssis.git'
    }
    stage('Create Sql Container') {
        bat label: 'Create container', script: 'docker run --name testdb2 -d -p 1434:1433 -e sa_password=ssisConfigPassword1 -e ACCEPT_EULA=Y microsoft/mssql-server-windows-developer';
    }
	stage('Let Sql Server start up'){
		sleep 10;
	}
    stage('Create db inside container') {
        bat label: 'Set up DB', script: 'sqlcmd -S localhost,1434 -i ".\\5\\globomantics.sql" -U sa -P ssisConfigPassword1'
    }
    stage('Execute Package') {
        bat label: 'Executing package', script: 'dtexec /f .\\5\\Globomantics\\Package.dtsx /SET "\\"\\Package.Variables[User::IncomingFilePath]\\"";"\\"C:\\\\Code\\\\Incoming\\\\"" /SET "\\"\\Package.Connections[Target_Sql_Server_DB].Properties[Password]\\"";ssisConfigPassword1 /SET "\\"\\Package.Connections[Target_Sql_Server_DB].Properties[ServerName]\\"";"\\"localhost,1434\\"" '
    }
	stage('Clean-up')
	{
		bat label: 'Remove container', script: 'docker stop testdb2';
	}
 }