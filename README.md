# Gerenciamento-de-Arquivos

class SistemaArquivos:
    def __init__(self, tamanho_memoria_total, tamanho_bloco):
        self.tamanho_memoria_total = tamanho_memoria_total
        self.tamanho_bloco = tamanho_bloco
        self.total_blocos = tamanho_memoria_total // tamanho_bloco
        self.mapa_memoria = [None] * self.total_blocos

    def alocar_blocos(self, blocos_necessarios):
        """
        Aloca blocos contíguos na memória para um arquivo.
        """
        blocos_alocados = []
        blocos_livres_consecutivos = 0

        for i in range(self.total_blocos):
            if self.mapa_memoria[i] is None:
                blocos_livres_consecutivos += 1
                if blocos_livres_consecutivos == blocos_necessarios:
                    blocos_alocados.extend(range(i - blocos_livres_consecutivos + 1, i + 1))
                    for bloco in blocos_alocados:
                        self.mapa_memoria[bloco] = "alocado"
                    return blocos_alocados
            else:
                blocos_livres_consecutivos = 0

        return []

    def criar_arquivo(self, nome, tamanho):
        """
        Cria um novo arquivo no sistema de arquivos.
        """
        if tamanho <= 0:
            print("Tamanho inválido. O arquivo deve ter tamanho maior que zero.")
            return

        if self.obter_entrada_por_nome(nome):
            print("Já existe um arquivo ou diretório com esse nome.")
            return

        blocos_necessarios = (tamanho + self.tamanho_bloco - 1) // self.tamanho_bloco
        blocos_alocados = self.alocar_blocos(blocos_necessarios)
        if blocos_alocados:
            self.mapa_memoria.append({"nome": nome, "tipo": "arquivo", "tamanho": tamanho, "blocos": blocos_alocados})
            print(f"Arquivo '{nome}'tamanho'{tamanho} foi criado com sucesso.")
        else:
            print("Espaço insuficiente para alocar o arquivo.")

    def criar_diretorio(self, nome):
        """
        Cria um novo diretório no sistema de arquivos.
        """
        if self.obter_entrada_por_nome(nome):
            print("Já existe um arquivo ou diretório com esse nome.")
            return

        self.mapa_memoria.append({"nome": nome, "tipo": "diretorio", "conteudo": []})
        print(f"Diretório '{nome}' criado com sucesso.")

    
    def excluir_entrada(self, entrada):
        """
        Exclui uma entrada (arquivo ou diretório) do sistema de arquivos.
         """
        if entrada["tipo"] == "arquivo":
            for bloco in entrada["blocos"]:
                self.mapa_memoria[bloco] = None
        else:
            for subentrada in entrada["conteudo"]:
                self.excluir_entrada(subentrada)

        index = self.mapa_memoria.index(entrada)
        self.mapa_memoria.pop(index)


    def excluir_arquivo(self, nome):
        """
        Exclui um arquivo do sistema de arquivos.
        """
        entrada = self.obter_entrada_por_nome(nome)
        if entrada and entrada["tipo"] == "arquivo":
            self.excluir_entrada(entrada)
            print(f"Arquivo '{nome}' excluído com sucesso.")
        else:
            print(f"Arquivo '{nome}' não encontrado.")

    def excluir_diretorio(self, nome):
        """
        Exclui um diretório do sistema de arquivos.
        """
        entrada = self.obter_entrada_por_nome(nome)
        if entrada and entrada["tipo"] == "diretorio":
            self.excluir_entrada(entrada)
            print(f"Diretório '{nome}' excluído com sucesso.")
        else:
            print(f"Diretório '{nome}' não encontrado.")

    def listar_conteudo(self, entradas, nivel=0):
        """
        Lista as entradas (arquivos e subdiretórios) no sistema de arquivos.
        """
        for entrada in entradas:
            if isinstance(entrada, dict) and entrada["tipo"] == "arquivo":
                print("  " * nivel + f"Arquivo: {entrada['nome']}, Tamanho: {entrada['tamanho']} bytes")
            elif isinstance(entrada, dict) and entrada["tipo"] == "diretorio":
                print("  " * nivel + f"Diretório: {entrada['nome']}")
                self.listar_conteudo(entrada["conteudo"], nivel + 1)

    def listar_arquivos(self, entradas, nivel=0):
        """
        Lista os arquivos no sistema de arquivos.
        """
        def listar_arquivos(self, entradas=None, nivel=0):
        
          if entradas is None:
            entradas = self.mapa_memoria
        
        for entrada in entradas:
            if isinstance(entrada, dict) and entrada["tipo"] == "arquivo":
                print("  " * nivel + f"Arquivo: {entrada['nome']}, Tamanho: {entrada['tamanho']} bytes")
    def listar_diretorios(self, entradas, nivel=0):
        """
        Lista os diretórios no sistema de arquivos.
        """
        for entrada in entradas:
            if isinstance(entrada, dict) and entrada["tipo"] == "diretorio":
                print("  " * nivel + f"Diretório: {entrada['nome']}")
                self.listar_diretorios(entrada["conteudo"], nivel + 1)

    def listar_apenas_diretorios(self):
        """
        Lista apenas os diretórios no sistema de arquivos.
        """
        print("Diretórios no sistema de arquivos:")
        self.listar_diretorios(self.mapa_memoria)

    def obter_entrada_por_nome(self, nome):
        """
        Retorna a entrada (arquivo ou diretório) com o nome especificado.
        """
        for entrada in self.mapa_memoria:
            if isinstance(entrada, dict) and entrada["nome"] == nome:
                return entrada
        return None


def main():
    tamanho_memoria_total = 1024  # Tamanho total da memória física (em bytes)
    tamanho_bloco = 64  # Tamanho dos blocos (em bytes)

    fs = SistemaArquivos(tamanho_memoria_total, tamanho_bloco)

    while True:
        print("\nEscolha uma opção:")
        print("1 - Criar arquivo")
        print("2 - Criar diretório")
        print("3 - Excluir arquivo")
        print("4 - Excluir diretório")
        print("5 - Listar arquivos")
        print("6 - Listar diretórios")
        print("7 - Listar conteudo")
        print("8 - Sair ")
        escolha = int(input())

        if escolha == 1:
            nome = input("Digite o nome do arquivo: ")
            tamanho = int(input("Digite o tamanho do arquivo (em bytes): "))
            fs.criar_arquivo(nome, tamanho)
        elif escolha == 2:
            nome = input("Digite o nome do diretório: ")
            fs.criar_diretorio(nome)
        elif escolha == 3:
            nome = input("Digite o nome do arquivo a ser excluído: ")
            fs.excluir_arquivo(nome)
        elif escolha == 4:
            nome = input("Digite o nome do diretório a ser excluído: ")
            fs.excluir_diretorio(nome)
        elif escolha == 5:
            fs.listar_arquivos(fs.mapa_memoria) 
        elif escolha == 6:
            fs.listar_apenas_diretorios()
        elif escolha == 7:
            
            fs.listar_conteudo(fs.mapa_memoria)

        elif escolha == 8:
            break
        else:
            print("Opção inválida. Tente novamente.")


if __name__ == "__main__":
    main()
