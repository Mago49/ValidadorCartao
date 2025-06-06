// Função para limpar o número do cartão (remove tudo que não é dígito)
function limparNumero(numero) {
    return numero.replace(/\D/g, "");
}

// Função para validar o número do cartão usando o algoritmo de Luhn
function validarLuhn(numero) {
    let soma = 0;
    let alternar = false;
    for (let i = numero.length - 1; i >= 0; i--) {
        let n = parseInt(numero[i], 10);
        if (alternar) {
            n *= 2;
            if (n > 9) n -= 9;
        }
        soma += n;
        alternar = !alternar;
    }
    return (soma % 10) === 0;
}

// Tabela com as regras de cada bandeira
const regrasBandeiras = [
    // Mastercard: 16 dígitos, começa com 51 ou 55 ou 2221-2720
    {
        nome: "Mastercard",
        tamanhos: [16],
        prefixos: [
            { tipo: "fixo", valor: "51", digitos: 2 },
            { tipo: "fixo", valor: "55", digitos: 2 },
            { tipo: "range", de: 2221, ate: 2720, digitos: 4 }
        ]
    },
    // Voyager: 15 dígitos, começa com 8699
    {
        nome: "Voyager",
        tamanhos: [15],
        prefixos: [
            { tipo: "fixo", valor: "8699", digitos: 4 }
        ]
    },
    // Visa: 16 dígitos, começa com 4
    {
        nome: "Visa",
        tamanhos: [16],
        prefixos: [
            { tipo: "fixo", valor: "4", digitos: 1 }
        ]
    },
    // American Express: 15 dígitos, começa com 34 ou 37
    {
        nome: "American Express",
        tamanhos: [15],
        prefixos: [
            { tipo: "fixo", valor: "34", digitos: 2 },
            { tipo: "fixo", valor: "37", digitos: 2 }
        ]
    },
    // Diners Club: 14 dígitos, começa com 301, 305, 36 ou 38
    {
        nome: "Diners Club",
        tamanhos: [14],
        prefixos: [
            { tipo: "fixo", valor: "301", digitos: 3 },
            { tipo: "fixo", valor: "305", digitos: 3 },
            { tipo: "fixo", valor: "36", digitos: 2 },
            { tipo: "fixo", valor: "38", digitos: 2 }
        ]
    },
    // Discover: 16 dígitos, começa com 6011, 65 ou 644-649
    {
        nome: "Discover",
        tamanhos: [16],
        prefixos: [
            { tipo: "fixo", valor: "6011", digitos: 4 },
            { tipo: "fixo", valor: "65", digitos: 2 },
            { tipo: "range", de: 644, ate: 649, digitos: 3 }
        ]
    },
    // En Route: 15 dígitos, começa com 2014 ou 2149
    {
        nome: "En Route",
        tamanhos: [15],
        prefixos: [
            { tipo: "fixo", valor: "2014", digitos: 4 },
            { tipo: "fixo", valor: "2149", digitos: 4 }
        ]
    },
    // JCB: 16 dígitos, começa com 35
    {
        nome: "JCB",
        tamanhos: [16],
        prefixos: [
            { tipo: "fixo", valor: "35", digitos: 2 }
        ]
    },
    // HiperCard: 16 dígitos, começa com 38 ou 60
    {
        nome: "HiperCard",
        tamanhos: [16],
        prefixos: [
            { tipo: "fixo", valor: "38", digitos: 2 },
            { tipo: "fixo", valor: "60", digitos: 2 }
        ]
    },
    // Aura: 16 dígitos, começa com 50
    {
        nome: "Aura",
        tamanhos: [16],
        prefixos: [
            { tipo: "fixo", valor: "50", digitos: 2 }
        ]
    },
];

// Função principal para identificar a bandeira
function identificarBandeira(numeroOriginal) {
    const numero = limparNumero(numeroOriginal);

    for (const regra of regrasBandeiras) {
        if (!regra.tamanhos.includes(numero.length)) continue;
        for (const prefixo of regra.prefixos) {
            const inicio = numero.slice(0, prefixo.digitos);
            if (prefixo.tipo === "fixo" && inicio === prefixo.valor) {
                if (!validarLuhn(numero)) return "Número de cartão inválido";
                return `Bandeira: ${regra.nome}`;
            }
            if (prefixo.tipo === "range") {
                const n = parseInt(inicio, 10);
                if (n >= prefixo.de && n <= prefixo.ate) {
                    if (!validarLuhn(numero)) return "Número de cartão inválido";
                    return `Bandeira: ${regra.nome}`;
                }
            }
        }
    }
    return "Número de cartão inválido";
}

// Exemplos de uso:
// console.log(identificarBandeira("4111 1111 1111 1111")); // Bandeira: Visa
// console.log(identificarBandeira("6011 0000 0000 0004")); // Bandeira: Discover
// console.log(identificarBandeira("5011 1111 1111 1111")); // Bandeira: Aura
// console.log(identificarBandeira("8699 1234 5678 901"));  // Bandeira: Voyager (precisa ser Luhn válido)

Quando reconhecer a bandeira:
"Bandeira: <nome da bandeira>"
Exemplo:"Bandeira: Visa"

Quando o número não corresponde a nenhuma bandeira válida:
"Número de cartão inválido"
