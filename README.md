# calcular_peso
Entendi! Vamos ajustar o plano para incluir um banco de dados para armazenar os pesos dos móveis e uma API para interagir com o sistema. Vou descrever os passos necessários e fornecer exemplos para implementar uma solução que atenda às suas necessidades.

### Estrutura do Sistema

1. **Banco de Dados**: Usaremos um banco de dados SQLite para armazenar informações sobre os móveis e seus pesos.

2. **API**: Utilizaremos o Flask para criar uma API que permita inserir a quantidade de móveis e calcular o peso total de madeira, metal e plástico.

3. **Interface de Usuário**: A interface será uma API simples que você pode usar para interagir com o sistema.

### Passos para Implementação

#### 1. Criar o Banco de Dados

Primeiro, vamos criar o banco de dados SQLite e uma tabela para armazenar os móveis e seus pesos.

**Código para criar o banco de dados (`create_db.py`):**

```python
import sqlite3

def create_database():
    conn = sqlite3.connect('furniture.db')
    cursor = conn.cursor()
    
    # Criar tabela
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS furniture (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            model TEXT NOT NULL,
            wood_weight REAL NOT NULL,
            metal_weight REAL NOT NULL,
            plastic_weight REAL NOT NULL
        )
    ''')
    
    # Inserir alguns dados exemplo
    cursor.execute('''
        INSERT INTO furniture (model, wood_weight, metal_weight, plastic_weight)
        VALUES
        ('mesa', 15.0, 10.0, 5.0),
        ('cadeira', 5.0, 3.0, 2.0)
    ''')
    
    conn.commit()
    conn.close()

if __name__ == "__main__":
    create_database()
```

#### 2. Criar a API com Flask

Agora, vamos criar uma API simples usando Flask que permitirá inserir a quantidade de móveis e calcular o peso total.

**Código para a API (`app.py`):**

```python
from flask import Flask, request, jsonify
import sqlite3

app = Flask(__name__)

def get_furniture_weights(model):
    conn = sqlite3.connect('furniture.db')
    cursor = conn.cursor()
    cursor.execute('SELECT wood_weight, metal_weight, plastic_weight FROM furniture WHERE model = ?', (model,))
    result = cursor.fetchone()
    conn.close()
    return result

@app.route('/calculate_weights', methods=['POST'])
def calculate_weights():
    data = request.json
    model = data.get('model')
    quantity = data.get('quantity')
    
    weights = get_furniture_weights(model)
    if weights:
        wood_weight, metal_weight, plastic_weight = weights
        total_wood = wood_weight * quantity
        total_metal = metal_weight * quantity
        total_plastic = plastic_weight * quantity
        return jsonify({
            'total_wood': total_wood,
            'total_metal': total_metal,
            'total_plastic': total_plastic
        })
    else:
        return jsonify({'error': 'Model not found'}), 404

if __name__ == "__main__":
    app.run(debug=True)
```

#### 3. Testar a API

1. **Rodar o Banco de Dados**: Execute o script `create_db.py` para criar o banco de dados e preencher com alguns dados exemplo.

2. **Rodar a API**: Execute o script `app.py` para iniciar o servidor Flask.

3. **Testar a API**: Use ferramentas como `curl`, `Postman`, ou um cliente HTTP para testar a API. Exemplo de requisição POST com `curl`:

   ```bash
   curl -X POST http://127.0.0.1:5000/calculate_weights \
        -H "Content-Type: application/json" \
        -d '{"model": "mesa", "quantity": 10}'
   ```

   Isso deve retornar algo como:

   ```json
   {
       "total_wood": 150.0,
       "total_metal": 100.0,
       "total_plastic": 50.0
   }
   ```

### Melhorias Futuras

- **Validação de Dados**: Adicionar validação de entrada para garantir que os dados recebidos estejam no formato correto.
- **Interface Gráfica**: Adicionar uma interface gráfica ou web para facilitar a interação com a API.
- **Autenticação e Segurança**: Implementar autenticação e segurança para o acesso à API.

Se precisar de mais alguma coisa ou tiver outras questões, estou à disposição para ajudar!
