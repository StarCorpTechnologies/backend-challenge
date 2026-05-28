# Teste Técnico — Back-end .NET

**StarCorp Travel — Reserva de Passagens Aéreas**

---

## 1. Contexto

Construa o back-end de um sistema de **reserva de passagens aéreas**. O cliente pesquisa voos, escolhe um, indica um ou mais passageiros, paga, e pode cancelar.

---

## 2. Stack obrigatória

| Item | Tecnologia |
|---|---|
| Linguagem | C# |
| Plataforma | .NET 10 |
| Acesso a dados | Dapper (sem outros ORMs) |
| Banco | SQL Server |
| API | REST com ASP.NET Core |
| Testes | xUnit |
| Versionamento | Git, repositório público |

---

## 3. Modelagem

Você desenha o banco. Entregue scripts em `/db/schema.sql` (DDL) e `/db/seed.sql` (dados de exemplo). O domínio mínimo:

- **Cliente**
- **Companhia aérea**
- **Voo** com origem, destino, horários e assentos disponíveis por classe
- **Classe tarifária**: `Econômica` e `Executiva` (regras de preço e cancelamento diferentes)
- **Reserva** com **um ou mais passageiros**
- **Passageiro** (nome e documento)
- **Pagamento**

Como modelar (herança, single table, etc.) é decisão sua — justifique no README.

---

## 4. Endpoints

### 4.1 Buscar voos
```
GET /api/flights
```
Filtros opcionais combináveis: cidade origem, cidade destino, data, preço mínimo, preço máximo, classe tarifária. Com paginação.

### 4.2 Criar reserva
```
POST /api/bookings
```
Recebe cliente, voo, classe tarifária e lista de passageiros. Deve:
1. Validar que o cliente existe e está ativo
2. Validar que há assentos disponíveis na classe escolhida
3. Calcular o preço completo (regras na seção 5)
4. Persistir reserva e passageiros
5. Retornar a reserva com breakdown do cálculo

### 4.3 Consultar reserva
```
GET /api/bookings/{id}
```

### 4.4 Processar pagamento
```
POST /api/bookings/{id}/payment
```
Recebe o método: `CreditCard`, `Pix` ou `Boleto`. Cada um tem regra própria.

### 4.5 Cancelar reserva
```
POST /api/bookings/{id}/cancel
```
Calcula o reembolso conforme a política da classe tarifária.

---

## 5. Regras de negócio

### 5.1 Composição do preço

```
Subtotal (preço base do voo × número de passageiros)
+ Impostos (8% + R$ 45 fixos por passageiro)
+ Taxa de serviço (5% sobre o subtotal com impostos)
± Ajuste do método de pagamento
= TOTAL
```

### 5.2 Classes tarifárias

| Classe | Multiplicador sobre o preço base |
|---|---|
| Econômica | 1,0x |
| Executiva | 2,5x |

### 5.3 Métodos de pagamento

| Método | Ajuste |
|---|---|
| Cartão de Crédito | +3% sobre o total |
| Pix | −5% (desconto) |
| Boleto | +1% sobre o total |

### 5.4 Política de cancelamento

| Classe | > 7 dias | 2 a 7 dias | < 2 dias |
|---|---|---|---|
| Econômica | 100% | 50% | 0% |
| Executiva | 100% | 75% | 25% |

**Regra especial:** cancelamentos em até **24h após o pagamento** têm reembolso integral, independentemente da política.

---

## 6. Requisitos não-funcionais

- **Testes automatizados** cobrindo as regras de negócio (cálculo de preço e cancelamento)
- **Tratamento de erros** com códigos HTTP coerentes (400, 404, 409, 422…)
- **README** com:
  - Como rodar (banco, aplicação, testes)
  - Decisões técnicas que você tomou
  - O que faria diferente com mais tempo

---

## 7. O que avaliamos

- Organização e clareza da arquitetura
- SOLID e Clean Code aplicados onde fazem sentido
- Reutilização de código sem repetição desnecessária
- Qualidade e relevância dos testes
- Modelagem do banco e qualidade das queries
- API REST coerente (rotas, verbos, status codes)
- Tratamento de bordas e erros

---

## 8. Entrega

- Repositório Git público (ou com acesso compartilhado)
- README claro
- Scripts de banco
- Testes passando

**Prazo:** 7 dias corridos a partir do recebimento. Se precisar de mais por questões pessoais ou profissionais, é só avisar.

**Envio:** responda ao e-mail original com o link do repositório.

Dúvidas: **recrutamento@starcorp.com.br**. Para ambiguidades do enunciado, documente sua interpretação no README — interpretar requisitos faz parte do trabalho.

Boa sorte! 🚀
