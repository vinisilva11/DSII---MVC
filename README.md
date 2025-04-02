<?php
defined('BASEPATH') OR exit('No direct acess allowed');

class Professor extends CI_Controller {
    // Atributos privados 
    private $codigo;
    private $nome;
    private $cpf;
    private $tipo;
    private $estatus;

    // Getters dos atributos
    public function getCodigo()
    {
        return $this->codigo;
    }

    public function getNome()
    {
        return $this->nome;
    }

    public function getCpf()
    {
        return $this->cpf;
    }

    public function getTipo()
    {
        return $this->tipo;
    }

    public function getEstatus()
    {
        return $this->estatus;
    }

    // Setters dos atributos
    public function setCodigo($codigoFront)
    {
        $this->nome = $codigoFront;
    }

    public function setNome($nomeFront)
    {
        $this->nome = $nomeFront;
    }

    public function setCpf($cpfFront)
    {
        $this->nome = $cpfFront;
    }

    public function setEstatus($estatusFront)
    {
        $this->nome = $estatusFront;
    }

    public function inserir(){
        // Horário Inicial e Horário Final
        // recebidos via JSON e colocados em variáveis
        //Retornos possíveis:
        // 1 - Professor cadastrado corretamente (Banco)
        // 2 - Faltou informar o Nome (FrontEnd)
        // 3 - Faltou informar o CPF (FrontEnd)
        // 4 - Faltou infotrmar o Tipo (FrontEnd)
        // 5 - Professor já cadastrado no sistema
        // 6 - Houve algum problema no insert na tabela (Banco)

        try{
            // Dados recebidos via JSON
            // e colocados em atributos
            $json = file_get_contents('php://input');
            $resultado = json_decode($json);

            // Array com os dados que deverão vir do Front
            $lista = array(
                "nome" = '0',
                "cpf" = '0',
                "tipo" = '0',
            );

            if (verificarParam($resultado, $lista) == 1) {
                // Fazendo os setters
                $this->setNome($resultado->nome);
                $this->setCpf($resultado->cpf);
                $this->setTipo($resultado->tipo);

                // Faremos uma validação para sabermos se todos os dados
                // foram enviados
                if (trim($this->getNome()) == '') {
                    $retorno = array('codigo' => 2,
                                    'msg' => 'Nome não informado.');
                }elseif (trim($this->getCpf()) == ''){
                    $retorno = array('codigo' => 3,
                                    'msg' => 'CPF não informado.');
                }elseif(trim($this->getTipo()) == ''){
                    $retorno = array('codigo' => 4,
                                    'msg' => 'Tipo não informado');
                }else{
                    //Realizo a instância da Model
                    $this->load->model('M_professor');

                    // Atributo $retorno recebe array com informações
                    //da validação do acesso
                    $retorno = $this->M_professor->inserir($this->getNome(),
                                                           $this->getTipo(),
                                                           $this->getCpf());
                }
            } else {
                $retorno = array(
                    'codigo' = 99,
                    'msg' => 'Os campos vindos FrontEnd não representam
                              o método de inserção. Verifique.'
                );
            }
        }catch (Exception $e) {
            $retorno = array('codigo' => 0,
                             'msg' => 'ATENÇÃO: O seguinte erro aconteceu -> ',
                                       $e->getMessage());
        }

        // Retorno no formato JSON
        echo json_encode($retorno);

    }

    public function consultar(){
        //Código, Nome, CPF e Tipo
        //recebido via JSON e colocados
        //em variáveis
        //Retornos possíveis:
        // 1 - Dados consultados corretamente (Banco)
        // 6 - Dados não encontrados (Banco)

        try{
            $json = file_get_contents('php://input');
            $resultado = json_decode($json);

            // Fazendo os setters
            $this->setCodigo($resultado->codigo);
            $this->setNome($resultado->nome);
            $this->setCpf($resultado->cpf);
            $this->setTipo($resultado->tipo);

            $lista = array(
                "codigo" = '0',
                "nome" = '0',
                "cpf" = '0',
                "tipo" = '0'
            );

            if (verificarParam($resultado, $lista) == 1) {
                // Realizo a instância da Model
                $this->load->model('M_professor');

                // Atributo $retorno recebe array com informações
                // da consulta dos dados
                $retorno = $this->M_professor->consultar($this->getCodigo(),
                                                        $this->getNome(),
                                                        $this->getCpf()
                                                        $this->getTipo());
            } else {
                $retorno = array(
                    'codigo' = 99,
                    'msg' => 'Os campos vindos FrontEnd não representam
                              o método de inserção. Verifique.'
                );
            }
        } catch (Exception $e) {
            $retorno = array('codigo' => 0,
                             'msg' => 'ATENÇÃO: O seguinte erro aconteceu -> ',
                                       $e->getMessage());
        }

        // Retorno no formato JSON
        echo json_encode($retorno);
    }

    public function alterar() {
        // Código, Nome, CPF e Tipo
        // recebidos via JSON e colocados
        // em variáveis
        // Retornos possíveis:
        // 1 - Dado(s) alterado(s) corretamente (Banco)
        // 2 - Código não informado ou Zerado
        // 3 - Pelo menos em parâmetro deve ser passado
        // 5 - Dados não encontrados (Banco)
        
        try{
            $json = file_get_contents('php://input');
            $resultado = json_decode($json);

            $lista = array(
                "codigo" = '0',
                "nome" = '0',
                "cpf" = '0',
                "tipo" = '0'
            );

            if (verificarParam($resultado, $lista) == 1) {
                // Fazendo os setters
                $this->setCodigo($resultado->codigo);
                $this->setNome($resultado->nome);
                $this->setCpf($resultado->cpf);
                $this->setTipo($resultado->tipo);

                // Validações para passagem de atributo ou campo VAZIO
                if (trim($this->getCodigo()) == '') {
                    $retorno = array('codigo' => 2,
                                    'msg' => 'Código não informado.');
                 // Nome, Senha ou Tipo de Usuário, pelo menos 1 deles precisa ser informado.                   
                }elseif(trim($this->getNome() == '') && trim($this->getTipo()) == ''
                        && trim($this->getCpf() == '')){
                    $retorno = array('codigo' => 3,
                                      'msg' => 'Pelo menos um parâmetro precisa ser
                                      passado para atualização.');          
                }else {
                    // Realizo a instância da Model
                    $this->load->model('M_professor');

                    // Atributo  $retorno recebe array com informações
                    // da alteração dos dados
                    $retorno = $this->M_professor->inserir($this->getCodigo(),
                                                           $this->getNome(),
                                                           $this->getCpf()
                                                           $this->getTipo());,
                                                           
                } 
            } else {
                $retorno = array(
                    'codigo' = 99,
                    'msg' => 'Os campos vindos FrontEnd não representam
                              o método de alterar. Verifique.'
                );
            }
        } catch (Exception $e) {
            $retorno = array('codigo' => 0,
                             'msg' => 'ATENÇÃO: O seguinte erro aconteceu -> ',
                                       $e->getMessage());
        }
        // Retorno no formato JSON
        echo json_encode($retorno);
    }

    public function desativar(){
        // Usuário recebido via JSON e colocado em variável
        // Retornos possiveis:
        // 1 - Horário desativado corretamente (Banco)
        // 2 - Código do horário não informado
        // 5 - Houve um problema na desativação do horário
        // 6 - Dados não encontrados (Banco)

        try{
            $json = file_get_contents('php://input');
            $resultado = json_decode($json);

            $lista = array(
                "codigo" = '0'
            );

            if (verificarParam($resultado, $lista) == 1) {
                
                $json = file_get_contents('php://input');
                $resultado = json_decode($json);
                
                // Fazendo os setters
                $this->setCodigo($resultado->codigo);

                // Validação para do usuário que não deverá ser banco
                if (trim($this->getCodigo() == '')) {
                    $retorno = array('codigo' => 2,
                                    'msg' => 'Código não informado.');
                }else{
                    // Realizo a instância da Model
                    $this->load->model('M_professor');

                    // Atributo $retorno array com informações
                    $retorno = $this->M_professor->desativar($this->getCodigo());
                }
            }else {
                $retorno = array(
                    'codigo' = 99,
                    'msg' => 'Os campos vindos FrontEnd não representam
                              o método de desativar. Verifique.'
                );
            }
    }catch (Exception $e) {
        $retorno = array('codigo' => 0,
                         'msg' => 'ATENÇÃO: O seguinte erro aconteceu -> ',
                                   $e->getMessage());
    }
    // Retorno no formato JSON
    echo json_encode($retorno);


}
