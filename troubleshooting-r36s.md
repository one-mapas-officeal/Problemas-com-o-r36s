# Troubleshooting: R36S / R36H (PortMaster e Wi-Fi via OTG)

Guia com os dois problemas mais comuns relatados no canal #r36s-r36h-pic.

---

## 1. PortMaster trava ao abrir (AttributeError no harbour.py)

### Sintoma

Ao abrir o menu do PortMaster, a tela mostra um traceback terminando em:

```
AttributeError: 'list' object has no attribute 'endswith'
```

O erro aparece em `pylibs/harbourmaster/harbour.py`, linha 959, na função `match_requirements`, durante a montagem da lista de "featured ports". Um port como `stargrovescramble.zip` aparece no log.

### Causa provável

O código espera que o campo `runtime` seja uma string (por exemplo, `algo.squashfs`), mas recebe uma lista vazia `[]`. Isso costuma acontecer quando:

- o port tem um arquivo de descrição em formato diferente do esperado pela versão instalada do PortMaster;
- o download ou a cópia do port ficou incompleto ou corrompido;
- a versão do PortMaster está desatualizada em relação aos ports mais novos.

### Solução

1. Remova o port problemático da pasta de ports (geralmente `roms/ports`): o arquivo `.zip` e o `.sh` correspondente.
2. Se o menu continuar travando, substitua a pasta `PortMaster` por uma versão nova, baixada do repositório oficial. O atualizador interno não funciona se o menu não abre.
3. Se quiser o port de volta, baixe-o novamente de uma fonte confiável e confira se o arquivo não está corrompido.
4. Se o problema persistir, envie o arquivo de log da pasta `PortMaster` para análise.

---

## 2. Wi-Fi não conecta (apenas interface `lo` aparece)

### Sintoma

Na tela de configuração de rede aparece somente:

```
CONNECTION  lo
ADDRESS     127.0.0.1/8
GATEWAY     (vazio)
```

Isso acontece com e sem o adaptador USB via OTG.

### Causa provável

O sistema não detecta nenhuma placa de rede real. Só existe a interface `lo` (loopback), que é interna. Possíveis motivos:

- **Falta de driver** para o chipset do adaptador USB (comum em chips Realtek RTL8188, RTL8192, RTL8812 etc.).
- **Modelo sem Wi-Fi integrado**: muitos R36S/R36H não têm Wi-Fi nativo.
- **Energia insuficiente** na porta OTG para o adaptador.

### Como investigar

- Com acesso a terminal (SSH ou ferramenta de diagnóstico), rode `lsusb` após conectar o adaptador. Se ele não aparecer, o problema é hardware ou energia. Se aparecer um ID sem driver, o problema é driver.
- Rode `dmesg | tail -50` logo após plugar o adaptador para ver mensagens de erro do kernel.
- Teste o adaptador em um PC ou celular com OTG. Se funcionar lá, o problema é compatibilidade com o sistema do aparelho.
- Use um hub USB com alimentação externa para descartar falta de energia.
- Confirme se a imagem do sistema instalado (ArkOS, dArkOS, Knulli etc.) é recente e compatível com o modelo.

---

## Informações úteis para pedir ajuda

Ao pedir ajuda no canal, inclua:

- modelo exato do aparelho;
- sistema instalado e versão;
- versão do PortMaster;
- chipset do adaptador Wi-Fi (aparece no conector ou no ID do `lsusb`);
- saída de `lsusb` e `dmesg` quando possível.
