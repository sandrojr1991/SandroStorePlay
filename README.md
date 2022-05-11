<?php

    const CHAVE_SECRETA =  '123456789';
    const LOCAL_ARQUIVO = '../arquivo/';


    if(!isset($_GET['arquivo']) || !isset($_GET['hash'])){
        echo 'Informações insuficientes';
        exit;
    }

    $nomeArquivo = $_GET['arquivo'];
    $hashNome = $_GET['hash'];

    if(!hash_equals(hash_hmac('SHA512', $nomeArquivo, CHAVE_SECRETA), $hashNome)){
        echo 'Algum parâmetro foi alterado!';
        exit;
    }

    if (!preg_match('/^(?:[a-z0-9_-]|\.(?!\.))+$/iD', $nomeArquivo)) {
        echo 'Arquivo é inválido';
        exit;    
        // Isto é feito para evitar ataques para ler aquivos internos (ex. "../etc/"...), na pior das hipóteses.
    }

    if (!file_exists(LOCAL_ARQUIVO.$nomeArquivo)) {
        echo 'Arquivo é inexistente';
        exit;
    }

    header('Content-Description: File Transfer');
    header('Content-Disposition: attachment; filename="'.$nomeArquivo.'"');
    header('Content-Type: application/octet-stream');
    header('Content-Transfer-Encoding: binary');
    header('Content-Length: ' . filesize(LOCAL_ARQUIVO.$nomeArquivo));
    header('Cache-Control: must-revalidate, post-check=0, pre-check=0');
    header('Pragma: public');
    header('Expires: 0');

    readfile(LOCAL_ARQUIVO.$nomeArquivo);
