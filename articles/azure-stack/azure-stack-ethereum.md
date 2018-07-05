---
title: Šablony řešení Azure Stack Etherea blockchain
description: Použití šablon vlastní řešení k nasazení a konfiguraci sítě konsorcia Etherea blockchain v Azure stacku
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 07/03/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 0e03b524834f528ddb7555a344fbebe720b4d9ff
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446967"
---
# <a name="azure-stack-ethereum-blockchain-solution-templates"></a>Šablony řešení Azure Stack Etherea blockchain

Šablona řešení Etherea je navržena pro snadnější a rychlejší nasazení a konfiguraci sítě konsorcia několika člen Etherea blockchain s minimálními znalostmi Azure a Etherea.

Pomocí několika vstupů uživatele a nasazení jedním kliknutím pomocí portálu Azure Stack tenanta můžete zřídit každý člen jejich nároky na síť. Nároky na síť každého člena se skládá ze sady uzlů s vyrovnáváním zatížení transakce s které aplikace nebo uživatele mohou spolupracovat při odeslání transakce, sada uzlů dolování se záznamu transakcemi a síťové virtuální zařízení (NVA). Krok následné připojení připojí síťových virtuálních zařízení pro vytvoření plně nakonfigurovaného blockchain více členy sítě.

## <a name="prerequisites"></a>Požadavky

Stáhněte si následující [z Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS verze 16.04.201802220
* Windows Server 2016 
* Vlastní skript pro Linux 2.0 
* Rozšíření vlastních skriptů 

Další informace o scénářích blockchain v Azure najdete v části [šablonu řešení ethereum během testování pracovní consortium](../blockchain-workbench/ethereum-deployment-guide.md).

Vyžaduje se předplatné Azure, který podporuje nasazení několik virtuálních počítačů. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="deployment-architecture"></a>Architektura nasazení

Tato šablona řešení můžete nasadit jednu nebo více sítě konsorcia Etherea člena. V řetězu topologii s použitím virtuální síťové zařízení a připojení prostředků připojení virtuální sítě. 

## <a name="deployment-use-cases"></a>Použití nasazené služby

Šablony můžete nasadit Etherea consortium pro vedoucí instancí a člen spojení v mnoha různými způsoby, tady jsou ty, které jsme testovali:
- V Azure několika uzly stacku s využitím Azure AD nebo AD FS, nasazení potenciálních zákazníků a člen pomocí stejného předplatného nebo různých předplatných.
- V Azure Stack jedním uzlem (s Azure AD) nasaďte potenciálních zákazníků a člen pomocí stejného předplatného.

### <a name="standalone-and-consortium-leader-deployment"></a>Samostatné a consortium vedoucí nasazení

Vedoucí instancí šablony consortium nakonfiguruje první člen nároky na místo v síti. 

1. Stáhněte si [vedoucí instance šablony z Githubu](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. V portálu pro správu Azure Stack, vyberte **nový > nasazení šablony** nasadit z vlastní šablony.
3. Vyberte **úpravy šablony** upravit novou vlastní šablonu.
4. V editovacím podokně na pravé straně zkopírujte a vložte vedoucí instancí šablony JSON, které jste předtím stáhli.
    
    ![Úprava vedoucí instance šablony](media/azure-stack-ethereum/edit-leader-template.png)

5. Vyberte **Uložit**.
6. Vyberte **upravit parametry** a dokončete parametry šablony pro vaše nasazení.
    
    ![Upravit parametry šablony vedoucí instancí](media/azure-stack-ethereum/edit-leader-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Řetězec použitý jako základ pro vytváření názvů nasazených prostředků. | Alfanumerické znaky o délce 1 až 6 | eth
    TYP AUTHTYPE | Metodu k ověření k virtuálnímu počítači. | Heslo nebo SSH veřejný klíč | Heslo
    ADMINUSERNAME | Uživatelské jméno správce každé nasazení virtuálního počítače | 1 – 64 znaků. | gethadmin
    ADMINPASSWORD (typ ověřování = heslo)| Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat 3 z těchto požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslici a 1 speciální znak. <br />Všechny virtuální počítače mají zpočátku stejné heslo, můžete změnit heslo po zřízení.|12 – 72 znaků|
    ADMINSSHKEY (typ ověřování = sshPublicKey) | Klíč zabezpečeného prostředí použitý ke vzdálenému přihlášení. | |
    GENESISBLOCK | Řetězec JSON představující vlastní genesis bloku.  Zadání hodnoty pro tento parametr je volitelný. | |
    ETHEREUMACCOUNTPSSWD | Heslo správce používá k zabezpečení účtu Etherea. | |
    ETHEREUMACCOUNTPASSPHRASE | Heslo se vygenerovat privátní klíč spojený s účtem Etherea. | |
    ETHEREUMNETWORKID | ID sítě konsorcia. | Použít libovolnou hodnotu mezi 5 a 999 999 999 | 72
    CONSORTIUMMEMBERID | ID přidružené k každý člen sítě konsorcia.   | Toto ID musí být jedinečné v síti. | 0
    NUMMININGNODES | Počet uzlů dolování. | Mezi 2 a 15. | 2
    MNNODEVMSIZE | Velikost virtuálního počítače pro uzly dolování. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Výkon úložiště uzlů dolování. | | Standard_LRS
    NUMTXNODES | Počet uzlů transakce. | Od 1 do 5. | 1
    TXNODEVMSIZE | Velikost virtuálního počítače pro uzly transakce. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Výkon úložiště uzlů transakce. | | Standard_LRS
    BASEURL | Základní adresa URL pro získání podle šablon z. | Pokud chcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. V **vlastní nasazení**, zadejte **předplatné**, **skupiny prostředků**, a **umístění skupiny prostředků**.
    
    ![Parametry nasazení vedoucí instancí](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Předplatné, pro které má být nasazení sítě konsorcia | | Využití předplatného
    Skupina prostředků | Skupina prostředků, do které chcete nasadit sítě konsorcia. | | EthereumResources
    Umístění | Oblast Azure pro skupinu prostředků. | | místní

8. Vyberte **Vytvořit**.

Nasazení může trvat 20 minut nebo i delší dobu pro dokončení.

Po dokončení nasazení můžete zkontrolovat shrnutí nasazení **Microsoft. Šablona** v části nasazení skupiny prostředků. Tento souhrn obsahuje výstupní hodnoty se dají přidat členy consortium.

Pokud chcete ověřit nasazení vedoucí, projděte mezi – web pro správu. Adresa webu správce najdete v části výstupu **Microsoft.Template** nasazení.  

![Souhrn nasazení vedoucí instancí](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Spojování consortium člen nasazení

1. Stáhněte si [consortium členské šablony z Githubu](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. V portálu pro správu Azure Stack, vyberte **nový > nasazení šablony** nasadit z vlastní šablony.
3. Vyberte **úpravy šablony** upravit novou vlastní šablonu.
4. V editovacím podokně na pravé straně zkopírujte a vložte vedoucí instancí šablony JSON, které jste předtím stáhli.
5. Vyberte **Uložit**.
6. Vyberte **upravit parametry** a dokončete parametry šablony pro vaše nasazení.

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Řetězec použitý jako základ pro vytváření názvů nasazených prostředků. | Alfanumerické znaky o délce 1 až 6 | eth
    TYP AUTHTYPE | Metodu k ověření k virtuálnímu počítači. | Heslo nebo SSH veřejný klíč | Heslo
    ADMINUSERNAME | Uživatelské jméno správce každé nasazení virtuálního počítače | 1 – 64 znaků. | gethadmin
    ADMINPASSWORD (typ ověřování = heslo)| Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat 3 z těchto požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslici a 1 speciální znak. <br />Všechny virtuální počítače mají zpočátku stejné heslo, můžete změnit heslo po zřízení.|12 – 72 znaků|
    ADMINSSHKEY (typ ověřování = sshPublicKey) | Klíč zabezpečeného prostředí použitý ke vzdálenému přihlášení. | |
    CONSORTIUMMEMBERID | ID přidružené k každý člen sítě konsorcia.   | Toto ID musí být jedinečné v síti. | 0
    NUMMININGNODES | Počet uzlů dolování. | Mezi 2 a 15. | 2
    MNNODEVMSIZE | Velikost virtuálního počítače pro uzly dolování. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Výkon úložiště uzlů dolování. | | Standard_LRS
    NUMTXNODES | Počet uzlů transakce. | Od 1 do 5. | 1
    TXNODEVMSIZE | Velikost virtuálního počítače pro uzly transakce. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Výkon úložiště uzlů transakce. | | Standard_LRS
    CONSORTIUMDATA | Adresa URL odkazující na konfigurační data relevantní consortium poskytované jiný člen nasazení. Tuto hodnotu můžete najít na vedoucí nasazení výstup. | |
    REMOTEMEMBERVNETADDRESSSPACE | Síťové virtuální zařízení IP adresa se vedoucí instancí. Tuto hodnotu můžete najít na vedoucí nasazení výstup. | | 
    REMOTEMEMBERNVAPUBLICIP | Síťové virtuální zařízení IP adresa se vedoucí instancí. Tuto hodnotu můžete najít na vedoucí nasazení výstup. | | 
    CONNECTIONSHAREDKEY | Předem zavedené tajný kód mezi členy sítě konsorcia, které jsou navazování připojení. | |
    BASEURL | Základní adresa URL pro šablonu. | Pokud chcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. V **vlastní nasazení**, zadejte **předplatné**, **skupiny prostředků**, a **umístění skupiny prostředků**.

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Předplatné, pro které má být nasazení sítě konsorcia | | Využití předplatného
    Skupina prostředků | Skupina prostředků, do které chcete nasadit sítě konsorcia. | | MemberResources
    Umístění | Oblast Azure pro skupinu prostředků. | | místní

8. Vyberte **Vytvořit**.

Nasazení může trvat 20 minut nebo i delší dobu pro dokončení.

Po dokončení nasazení můžete zkontrolovat shrnutí nasazení **Microsoft.Template** v části nasazení skupiny prostředků. Tento souhrn obsahuje výstupní hodnoty, které slouží k připojení consortium členy.

Pokud chcete ověřit nasazení člena, procházení webu Správce člena. Adresa webu správce najdete v části výstupů Microsoft.Template nasazení.

![Souhrn nasazení člena](media/azure-stack-ethereum/ethereum-node-status-2.png)

Jak je znázorněno na obrázku, je stav uzlů člena **neběží**. Je to proto, že nedojde k připojení mezi členem a vedoucí instancí. Připojení mezi členem a vedoucí instance je obousměrné připojení. Při nasazování členské šablony automaticky vytvoří připojení od člena, na vedoucí instancí. Chcete-li vytvořit připojení z lídr na člen přejděte k dalšímu kroku.

### <a name="connect-member-and-leader"></a>Připojení člena a leader

Tato šablona vytvoří připojení z vedoucí na vzdálený člen. 

1. Stáhněte si [připojení člen a vedoucí instance šablony z Githubu](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. V portálu pro správu Azure Stack, vyberte **nový > nasazení šablony** nasadit z vlastní šablony.
3. Vyberte **úpravy šablony** upravit novou vlastní šablonu.
4. V editovacím podokně na pravé straně zkopírujte a vložte vedoucí instancí šablony JSON, které jste předtím stáhli.
    
    ![Upravit připojení šablony](media/azure-stack-ethereum/edit-connect-template.png)

5. Vyberte **Uložit**.
6. Vyberte **upravit parametry** a dokončete parametry šablony pro vaše nasazení.
    
    ![Upravit připojení parametry šablony](media/azure-stack-ethereum/edit-connect-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Předpona názvu vedoucí. Tuto hodnotu můžete najít na vedoucí nasazení výstup.  | Alfanumerické znaky o délce 1 až 6 | |
    MEMBERROUTETABLENAME | Název tabulky směrování se vedoucí instancí. Tuto hodnotu můžete najít na vedoucí nasazení výstup. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Adresní prostor člena. Tuto hodnotu můžete najít na člena nasazení výstup. | |
    CONNECTIONSHAREDKEY | Předem zavedené tajný kód mezi členy sítě konsorcia, které jsou navazování připojení.  | |
    REMOTEMEMBERNVAPUBLICIP | Síťové virtuální zařízení IP adresu člena. Tuto hodnotu můžete najít na člena nasazení výstup. | |
    MEMBERNVAPRIVATEIP | Vedoucí privátní adresu IP síťového virtuálního zařízení. Tuto hodnotu můžete najít na vedoucí nasazení výstup. | |
    UMÍSTĚNÍ | Umístění prostředí Azure Stack. | | místní
    BASEURL | Základní adresa URL pro šablonu. | Pokud chcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. V **vlastní nasazení**, zadejte **předplatné**, **skupiny prostředků**, a **umístění skupiny prostředků**.
    
    ![Připojte se parametry nasazení](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Předplatné se vedoucí instancí. | | Využití předplatného
    Skupina prostředků | Skupina prostředků se vedoucí instancí. | | EthereumResources
    Umístění | Oblast Azure pro skupinu prostředků. | | místní

8. Vyberte **Vytvořit**.

Po dokončení nasazení trvá několik minut, než se vedoucí instancí a člen komunikaci. Pokud chcete ověřit nasazení, aktualizujte člena – web pro správu. Stav členskými uzly by měl být spuštěn. 

![Ověření nasazení](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Další postup

- Další informace o Ethereem a Azure, najdete v článku [technologie Blockchainu a aplikace | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Další informace o scénářích blockchain v Azure najdete v části [šablonu řešení ethereum během testování pracovní consortium](../blockchain-workbench/ethereum-deployment-guide.md).