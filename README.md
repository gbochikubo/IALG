# IALG
Trab_Final
// Trabalho de IALG, programa com tema: bandas de rock //

#include <iostream>
#include <fstream>
#include <string>
#include <cmath>
#include <cstdlib>
#include <cstring> 
using namespace std;

// Registro com as informações da banda //

struct bandas{
	char nome_banda[300];
	int num_integrantes;
	char estilo_musical [300];
	int ano_fundacao;
	char musica1 [300];
	char musica2 [300];
};

// Função responsavel pela leitura de novas bandas //
/** A função faz a leitura de novas bandas até que o usuario nao queira inserir mais nenhuma banda digitando 'n' **/

void cadastro (ofstream *arquivo,int &cont,bandas vet[]){
	char control = 's'; 				 // Variavel responsavel por controlar a inserção de novas bandas // 
	while(control == 's'){
		bandas dados;
		cout << "Nome da Banda: ";
		cin.ignore();
		cin.getline(dados.nome_banda,300);
		cout << "Numero de integrantes: ";
		cin >> dados.num_integrantes;
		cout << "Estilo Musical: ";
		cin.ignore();
		cin.getline(dados.estilo_musical,300);
		cout << "Ano em que foi fundada: ";
		cin >> dados.ano_fundacao;
		cout << "Musica famosa um: ";
		cin.ignore();
		cin.getline(dados.musica1,300);
		cout << "Musica famosa dois: ";
		
		cin.getline(dados.musica2,300);
		
		cout << dados.musica2 << endl;
		vet[cont]=dados;
		cont++;
		system("clear");
		cout << "Banda cadastrada!" << endl;
		(*arquivo).write (reinterpret_cast<const char *>(&dados), sizeof(dados));
		cout << "Deseja cadastrar mais alguma banda ?	(s/n): ";
		cin >> control; 
	}
}

// Funçao Responsavel por buscar determinada banda //
/** A função percorre o arquivo para determinar o seu tamanho em bytes
 * Logo após o tamanho encontrado em bytes é dividido pelo tamanhado de cada struct 'bandas'
 * para determinar o número de structs contidas dentro do arquivo
 * após isso os dados são passados para um vetor e a buscar é realizada **/

void busca (ifstream *arquivo,char buscada[],int &pos){
	system("clear");
	(*arquivo).seekg(0, (*arquivo).end);
	int bytes = (*arquivo).tellg();			// Variável responsável por receber o tamanho de bytes do arquivo //
	(*arquivo).seekg(0,(*arquivo).beg);
	int tam= bytes/ sizeof(bandas);			// Variável responável por receber o numero exato de structs dentro do programa //					
	bandas vetor[tam];
	arquivo->read ((char*) (vetor), bytes);
	bool achou=false;
	for(int i=0;i<tam;i++){
		if (strcmp(buscada, vetor[i].nome_banda) == 0){
			pos=i;
			cout << "Nome: " << vetor[i].nome_banda << endl << "Numero de Integrantes: " <<  vetor[i].num_integrantes << endl;
			cout << "Estilo Musical: " << vetor[i].estilo_musical << endl << "Ano da fundacao: " << vetor[i].ano_fundacao << endl;
			cout << "Musica 1: "<< vetor[i].musica1 << endl <<"Musica 2: " << vetor[i].musica2 << endl;
			cout << endl;
			achou=true;
		}
		if((i==tam-1)and !(achou)){
			cout << "Banda não Encontrada" << endl;
		}
	}   
}

// Função Responsavel por mostrar os dados cadastrados //

void mostrar_dados(){
	bandas dados;
	ifstream arquivo("Bandas.dat",ios :: binary);
	arquivo.seekg(0, ios_base::beg);
	while(arquivo.read((char *) &dados, sizeof(bandas))){
		cout << "Nome: " << dados.nome_banda << endl;
		cout << "Numero de integrantes: " << dados.num_integrantes << endl;
		cout << "Estilo Musical: " << dados.estilo_musical << endl;
		cout << "Ano de fundacao: " << dados.ano_fundacao << endl;
		cout << "Musicas famosas: " << dados.musica1 << ", "<< dados.musica2 << endl << endl;
	}
	arquivo.close();
}

// Função Responsavel por ordenar as bandas de acordo com o numero de integrantes //
// Metodo bubblesort //

void bubble (ifstream *arquivo){
	bandas aux;
	(*arquivo).seekg(0, (*arquivo).end);
	int bytes = (*arquivo).tellg();
	(*arquivo).seekg(0,(*arquivo).beg);
	int tam= bytes/ sizeof(bandas);
	bandas vet[tam];
	arquivo->read ((char*) (vet), bytes);
	arquivo->close();
	for (int i=0;i<tam;i++){
		for(int j=0;j<tam-1;j++){
			if(vet[j].num_integrantes>vet[j+1].num_integrantes){
				aux=vet[j];
				vet[j]=vet[j+1];
				vet[j+1]=aux;
			}
		}
	}
	ofstream arquivos("Bandas.dat",ios::binary);
	arquivos.write (reinterpret_cast<const char *>(&vet), sizeof(vet));
	arquivos.close();
}

// Função para achar o struct // 
/** A função recebe a posição, percorre o arquivo e retorna o struct buscado **/

bandas proucurada (int posicao){
	ifstream arquivo ("Bandas.dat", ios :: binary);
	arquivo.seekg ((posicao)*sizeof(bandas),arquivo.beg);
	bandas atualizadas;
	arquivo.read((char*) &atualizadas, sizeof (bandas));
	arquivo.close();
	return atualizadas;
}
    
// Função responsavel por atualizar dados //
/** A função recebe o nome da banda a ser atualizada, em seguida faz uma busca para achar a função 
 * o usuario então escolhe o dado a ser atualizado e a atualização é feita **/

char atualiza (ifstream *arquivo,char buscada[],bandas vetor[]){
	char controlador='s';
	int p=0; 
	ifstream tamanho ("Bandas.dat");
	tamanho.seekg (0,tamanho.end);
	int bytes=tamanho.tellg();
	tamanho.seekg(0,tamanho.beg);
	int tam=bytes/sizeof(bandas);
	bandas vet[tam];
	tamanho.read((char*)(vet),bytes);
	tamanho.close();
	cout << "Qual dado deseja alterar ?" << endl << endl;
	char operacao;
	char novo_dado[300];
	int numero=0;
	busca(arquivo,buscada,p);
	bandas encontrada=proucurada(p);
	cout << "1- Nome da banda" << endl << "2- Numero de integrantes" << endl << "3- Estilo musical" << endl;
	cout << "4- Ano de fundaçao" << endl << "5- Musica 1" << endl << "6- Musica 2" << endl;
	cout << "Option: ";
	cin >> operacao;
	system("clear");
	if (operacao=='1'){
		cout << "Novo nome: ";
		cin.ignore();
		cin.getline(novo_dado,300);
		cout << endl;
		cout << "Nome atualizado!" << endl << endl;
		strcpy(encontrada.nome_banda,novo_dado);  
	}
	else if(operacao =='2'){
		cout << "Novo numero de integrantes: ";
		cin >> numero;
		cout << endl;
		cout << "Numero de integrantes atualizado" << endl << endl;
		encontrada.num_integrantes =numero;
	}
	else if(operacao =='3'){
		cout << "Novo estilo musical: ";
		cin.ignore();
		cin.getline(novo_dado,300);
		cout << endl;
		cout << "Estilo musical atualizado!" << endl << endl;
		strcpy(encontrada.estilo_musical,novo_dado);
	}
	else if(operacao == '4'){
		cout << "Novo ano de fundaçao: ";
		cin >> numero;
		cout << endl;
		cout << "Ano de fundação atualizado!" << endl << endl;
		encontrada.ano_fundacao=numero;
	}
	else if(operacao =='5'){
		cout << "Nova musica 1: ";
		cin.ignore();
		cin.getline(novo_dado,300);
		cout << endl;
		cout << "Musica 1 atualizada!" << endl << endl;
		strcpy(encontrada.musica1,novo_dado);
	}
	else if(operacao =='6'){
		cout <<"Nova musica 2: ";
		cin.ignore();
		cin.getline(novo_dado,300);
		cout << endl;
		cout << "Musica 2 atualizada!" << endl << endl;
		strcpy (encontrada.musica1,novo_dado);
	}
	else {
		cout << "Operação Invalida" << endl;
	}
		
	vet[p]=encontrada;
	ofstream escrever ("Bandas.dat", ios::binary);
	escrever.write(reinterpret_cast<const char*>(vet),sizeof(bandas)*tam);
	escrever.close();
	cout << "Deseja alterar mais algum dado ?	(s/n): ";
	cin >> controlador;
	system("clear");
	return controlador;
}

// Função para chamar o menu do programa //
/** A função mostra para o usuario os comandos disponiveis e recebe o comando escolhido **/

int menu(){
	int control;
	cout << "		Menu" << endl;
	cout << "1 - Inserir relatorios de bandas" << endl << "2 - Buscar Banda" << endl;
	cout << "3 - Atualizar bandas" << endl << "4 - Mostrar dados cadastrados" << endl << "0 - Encerrar programa" << endl;
	cout << "Option: ";
	cin >> control;
	return control;
}

// Função Principal //

int main ()
{
	cout << "Programa das bandas!" << endl;
	char resposta = 's', controlador;
	char buscada[300];
	int cont=0;		// Variavel responsavel por contar o numero de bandas //
	int posicao;	// Variavel responsavel por receber a posição da banda buscada //
	bandas vetor[100];
	while(resposta == 's'){
		controlador = menu();
		system("clear");
		cout << "Operação selecionada: ";
		if(controlador==1){
			cout << "Inserir relatorios de bandas" << endl << endl;
			ofstream arquivo ("Bandas.dat", ios::binary|ios::app);
			cadastro (&arquivo,cont,vetor);
			arquivo.close();
		}
		else if (controlador==2){
			cout << "Buscar banda" << endl << endl;
			cout << "Nome da Banda a ser procurada: "; 
			cin.ignore();
			cin.getline(buscada,300);
			ifstream arquivo ("Bandas.dat");
			busca(&arquivo,buscada,posicao);
			arquivo.close();
		}
		else if(controlador==3){
			cout << "Atualizar dados da banda" << endl << endl;
			controlador = 's';
			while(controlador == 's'){
				cout << "Nome da banda a ser atualizada : ";
				cin.ignore();
				cin.getline(buscada,300);
				system("clear");
				ifstream arquivo ("Bandas.dat");
				controlador = atualiza (&arquivo,buscada,vetor);
				arquivo.close();
			}
		}
		else if(controlador==4){
			cout << "Mostrar dados cadastrados" << endl << endl;
			ifstream arquivo ("Bandas.dat");
			bubble(&arquivo);
			arquivo.close();
			mostrar_dados();  
		}      
		else if(controlador==0){
			cout << "Encerrar programa" << endl << endl;
			cout << "Programa encerrado! Ate mais!" << endl;
			resposta = 'n';
		}
		if(controlador != 0){
			cout << "Deseja fazer mais alguma operacao ?	(s/n): ";
			cin >> resposta;
			system("clear");
			if(resposta == 'n'){
				cout << "Programa encerrado!	Ate mais!" << endl;
			}
		}
	}
	return 0;
}

