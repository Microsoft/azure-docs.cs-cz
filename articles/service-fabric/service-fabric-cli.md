---
title: Začínáme s Azure Service Fabric CLI
description: Naučte se používat Azure Service Fabric CLI. Zjistěte, jak se připojit ke clusteru a jak spravovat aplikace.
author: jeffj6123
ms.topic: conceptual
ms.date: 5/19/2020
ms.author: jejarry
ms.openlocfilehash: b3714f8401def9bed68e4b0845d025734a480cb3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83681798"
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric CLI

Rozhraní příkazového řádku (CLI) Azure Service Fabric je nástroj příkazového řádku pro práci s entitami Service Fabric a jejich správu. Service Fabric CLI lze použít s clustery s Windows nebo Linuxem. Service Fabric CLI funguje na libovolné platformě, kde je podporován Python.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Požadavky

Před instalací se ujistěte, že je ve vašem prostředí nainstalovaný Python a pip. Další informace najdete v [úvodní dokumentaci nástroje pip](https://pip.pypa.io/en/latest/quickstart/) a oficiální [dokumentaci k instalaci Pythonu](https://wiki.python.org/moin/BeginnersGuide/Download).

Rozhraní příkazového řádku podporuje Python verze 2,7, 3,5, 3,6 a 3,7. Python 3. x je doporučená verze, protože Python 2,7 se brzy dokončí.

### <a name="service-fabric-target-runtime"></a>Cílový modul runtime Service Fabric

Smyslem Service Fabric CLI je podpora nejnovější verze modulu runtime sady Service Fabric SDK. Pomocí následující tabulky určete, jakou verzi rozhraní příkazového řádku máte nainstalovat:

| Verze rozhraní příkazového řádku   | Podporovaná verze modulu runtime |
|---------------|---------------------------|
| Nejnovější (~ = 10) | Nejnovější (~ = 7,1)            |
| 9.0.0         | 7.1                       |
| 8.0.0         | 6.5                       |
| 7.1.0         | 6.4                       |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6,2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6,0                       |
| 1.1.0         | 5.6, 5.7                  |

Volitelně můžete zadat cílovou verzi rozhraní příkazového řádku, která se má nainstalovat, přidáním přípony `==<version>` k příkazu `pip install`. Například pro verzi 1.1.0 by syntax byla následující:

```shell
pip install -I sfctl==1.1.0
```

V případě potřeby nahraďte následující příkaz `pip install` dříve popsaným příkazem.

Další informace o vydaných verzích Service Fabric CLI najdete v [dokumentaci na GitHubu](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Instalace nástroje pip, Pythonu a Service Fabric CLI

Na své platformě můžete pip a Python nainstalovat mnoha způsoby. Tady je několik kroků, pomocí kterých můžete rychle nastavit Python 3 a pip na hlavních operačních systémech.

### <a name="windows"></a>Windows

Ve Windows 10, Windows Server 2016 a Windows Server 2012 R2 použijte standardní oficiální pokyny k instalaci. Instalační program Pythonu ve výchozím nastavení nainstaluje také pip.

1. Přejít na oficiální [stránku ke stažení v Pythonu](https://www.python.org/downloads/)a Stáhněte si nejnovější verzi pythonu 3,7.

2. Spusťte instalační program.

3. V dolní části výzvy vyberte **Přidat Python 3,7 k cestě**.

4. Vyberte **Install Now** (Nainstalovat) a dokončete instalaci.

Teď můžete otevřít nové příkazové okno a získat verze Pythonu i nástroje pip.

```shell
python --version
pip --version
```

Pak spuštěním následujícího příkazu nainstalujte rozhraní příkazového řádku Azure Service Fabric CLI (sfctl) a zobrazte stránku s přehledem CLI:

```shell
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu a subsystém Windows pro Linux

Pokud chcete nainstalovat Service Fabric CLI, spusťte následující příkazy:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Instalaci pak můžete otestovat pomocí:

```bash
sfctl -h
```

Pokud se zobrazí chyba typu příkaz nenalezen, například:

`sfctl: command not found`

Ujistěte se, že `$PATH` má přístup k `~/.local/bin`:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .shellrc
```

Pokud instalace na subsystému Windows pro Linux selže kvůli nesprávným oprávněním ke složce, možná bude nutné pokus opakovat se zvýšenými oprávněními:

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (podpora Service Fabric ve verzi Preview)

Pokud chcete nainstalovat Service Fabric CLI v Red Hat, spusťte následující příkazy:

```bash
sudo yum install -y python34
sudo yum install python34-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

Při testování instalace se můžete podívat na postup uvedený v části **Ubuntu a subsystém Windows pro Linux** .

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

V systému MacOS doporučujeme použít [správce balíčků HomeBrew](https://brew.sh). Pokud HomeBrew ještě není nainstalovaný, nainstalujte ho spuštěním následujícího příkazu:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Pak z terminálu nainstalujte Python 3,7, PIP a Service Fabric CLI spuštěním následujících příkazů:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>Syntaxe rozhraní příkazového řádku

Příkazy mají vždy předponu `sfctl`. Obecné informace o všech příkazech, které můžete použít, získáte pomocí příkazu `sfctl -h`. Nápovědu ke konkrétnímu příkazu získáte pomocí příkazu `sfctl <command> -h`.

Příkazy dodržují opakovatelnou strukturu, kdy cíl příkazu předchází operaci nebo akci.

```shell
sfctl <object> <action>
```

V tomto příkladu je `<object>` cílem pro `<action>`.

## <a name="select-a-cluster"></a>Výběr clusteru

Před provedením jakékoli operace musíte vybrat cluster, ke kterému se připojíte. Pokud například chcete vybrat cluster `testcluster.com` a připojit se k němu, spusťte následující příkaz:

> [!WARNING]
> Nepoužívejte nezabezpečené clustery Service Fabric v produkčním prostředí.

```shell
sfctl cluster select --endpoint http://testcluster.com:19080
```

Koncový bod clusteru musí mít předponu `http` nebo `https`. Musí zahrnovat port pro bránu HTTP. Tento port a adresa jsou stejné jako adresa URL nástroje Service Fabric Explorer.

Pro clustery, které jsou zabezpečené pomocí certifikátu, můžete určit certifikát kódovaný PEM. Certifikát lze zadat jako jeden soubor nebo jako pár certifikátu a klíče. Pokud je certifikát, který není podepsaný certifikační autoritou, podepsaný svým držitelem, můžete obejít ověření certifikační autority zadáním možnosti `--no-verify`.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Další informace najdete v tématu [Připojení k zabezpečenému clusteru Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Základní operace

Informace o připojení ke clusteru se uchovávají napříč více relacemi Service Fabric CLI. Po výběru clusteru Service Fabric na něm můžete spouštět jakékoli příkazy Service Fabric.

Pokud například chcete získat stav clusteru Service Fabric, použijte následující příkaz:

```shell
sfctl cluster health
```

Příkaz vrátí následující výstup:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Tipy a řešení potíží

Tady jsou některé návrhy a tipy pro řešení běžných problémů.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Převod certifikátu z formátu PFX na PEM

Service Fabric CLI podporuje certifikáty na straně klienta v podobě souborů PEM (s příponou .pem). Pokud používáte soubory PFX ze systému Windows, musíte tyto certifikáty převést na formát PEM. K převodu souboru PFX na soubor PEM použijte následující příkaz:

```shell
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Podobně k převodu ze souboru PEM do souboru PFX můžete použít následující příkazy (zde bez zadání hesla):

```shell
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Další informace najdete v [dokumentace k OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Problémy s připojením

Některé operace můžou generovat následující zprávu:

`Failed to establish a new connection`

Ověřte, že zadaný koncový bod clusteru je dostupný a naslouchá. Ověřte také, že je na daném hostiteli a portu dostupné uživatelské rozhraní Service Fabric Explorer. Pokud chcete aktualizovat koncový bod, použijte příkaz `sfctl cluster select`.

### <a name="detailed-logs"></a>Podrobné protokoly

Podrobné protokoly jsou často užitečné při ladění nebo hlášení problému. `--debug`Příznak zvyšuje úroveň podrobností výstupu.

### <a name="command-help-and-syntax"></a>Nápověda k příkazům a jejich syntaxe

Pokud chcete získat nápovědu ke konkrétnímu příkazu nebo skupině příkazů, použijte příznak `-h`.

```shell
sfctl application -h
```

Tady je další příklad:

```shell
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Aktualizace rozhraní příkazového řádku Service Fabric 

Pokud chcete aktualizovat rozhraní příkazového řádku Service Fabric, spusťte následující příkazy (podle volby provedené během původní instalace nahraďte `pip` pomocí `pip3`):

```shell
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Další kroky

* [Nasazení aplikace pomocí Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
* [Začínáme se Service Fabric v Linuxu](service-fabric-get-started-linux.md)
