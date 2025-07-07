
#include <HX711.h> //adiciona a biblioteca ao codigo

//configuracao dos pinos para o modulo HX711
const int PINO_DT = 19;
const int PINO_SCK = 23;

const int TEMPO_ESPERA = 1000; //declaracao da variavel de espera

HX711 escala; //declaracao do objeto escala na classe HX711 da biblioteca

float fator_calibracao = -368.5; //pre-definicao da variavel de calibracao

char comando; //declaracao da variavel que ira receber os comandos para alterar o fator de calibracao

void setup ()
{

  //mensagens do monitor serial
  Serial.begin(115200);
  Serial.println("Celula de carga - Calibracao de Peso");
  Serial.println("Posicione um peso conhecido sobre a celula ao comecar as leituras");

  escala.begin (PINO_DT, PINO_SCK); //inicializacao e definicao dos pinos DT e SCK dentro do objeto ESCALA

  //realiza uma media entre leituras com a celula sem carga 
  float media_leitura = escala.read_average(); 
  Serial.print("Media de leituras com Celula sem carga: ");
  Serial.print(media_leitura);
  Serial.println();

  escala.tare(); //zera a escala
}

void loop ()
{
  escala.set_scale(fator_calibracao); //ajusta a escala para o fator de calibracao

  //verifica se o modulo esta pronto para realizar leituras
  if (escala.is_ready())
  {
    //mensagens de leitura no monitor serial
    Serial.print("Leitura: ");
    Serial.print(escala.get_units(), 10); //retorna a leitura da variavel escala com a unidade quilogramas
    Serial.print(" g");
    Serial.print(" \t Fator de Calibracao = ");
    Serial.print(fator_calibracao);
    Serial.println();

  //alteracao do fator de calibracao
    if(Serial.available())
      {
        comando = Serial.read();
        switch (comando)
        {
          case 'x':
          fator_calibracao = fator_calibracao - 0.5;
          break;
          case 'c':
          fator_calibracao = fator_calibracao + 0.5;
          break;
          case 'v':
          fator_calibracao = fator_calibracao - 5;
          break;
          case 'b':
          fator_calibracao = fator_calibracao + 5;
          break;
          case 'n':
          fator_calibracao = fator_calibracao - 1;
          break;
          case 'm':
          fator_calibracao = fator_calibracao + 1;
          break;
           case 'k':
          fator_calibracao = fator_calibracao + 0.1;
          break;
           case 'a':
          fator_calibracao = fator_calibracao - 0.1;
          break;
        }
      }
    }
    else
    {
      Serial.print("HX-711 ocupado");
    }
  delay(TEMPO_ESPERA);
}
