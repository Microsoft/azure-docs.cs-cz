---
title: Šablona řešení Azure Ethereum zásobníku
description: Použití šablon vlastní řešení můžete nasadit a nakonfigurovat síť Ethereum consortium v Azure zásobníku
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 4c2b0cda2d4144cde733f7f57ac6311e1a69f547
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114724"
---
# <a name="azure-stack-ethereum-solution-templates"></a>Šablony řešení Azure Ethereum zásobníku

Šablona řešení Ethereum slouží ke snadnější a rychlejší nasazení a konfiguraci sítě Ethereum consortium více členy s minimálními znalostmi Azure a Ethereum.

Někteří z nich vstupy uživatele a jedním kliknutím nasazení pomocí portálu správce Azure zásobníku může každý člen zřizovat jejich nároky na síť. Každý člen sítě nároků obsahuje sadu uzlů s vyrovnáváním zatížení transakce s které aplikace nebo uživatele mohou komunikovat Odeslat transakce, sada uzlů dolování pro transakce záznamů a sítě virtuálního zařízení (hodnocení chyb zabezpečení). Krok následné připojení připojí NVAs vytvořit síť kompletně nakonfigurovaný blockchain více členy.

## <a name="prerequisites"></a>Požadavky

Stáhněte si následující [z Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS verze 16.04.201802220
* Windows Server 2016 
* Vlastní skript pro Linux 2.0 
* Rozšíření vlastních skriptů 

Další informace o scénářích blockchain v Azure najdete v tématu [šablona řešení ověření pracovní consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).

Předplatné Azure, který podporuje nasazení několik virtuálních počítačů je požadovaná. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="deployment-architecture"></a>Architektura nasazení služby

Tato šablona řešení můžete nasadit jeden nebo více členů Ethereum consortium sítě. Virtuální síť je připojená v řetězu topologie pomocí virtuální zařízení sítě a připojení prostředků. 

## <a name="deployment-use-cases"></a>Případy použití nasazení

Šablony můžete nasadit Ethereum consortium pro vedoucího a člen spojení v mnoha různými způsoby, tady jsou ty, které jsme testovali:
- V Azure několika uzly zásobníku s Azure AD ani AD FS, nasazení realizace a člena pomocí stejného předplatného nebo různých předplatných.
- V zásobníku Azure jedním uzlem (s Azure AD) nasaďte realizace a člena pomocí stejného předplatného.

### <a name="standalone-and-consortium-leader-deployment"></a>Samostatné a consortium vedoucí nasazení

Šablona vedoucí consortium nakonfiguruje první člen nároků v síti. 

1. Stažení [vedoucí šablony z Githubu](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/ConsortiumLeader/mainTemplate.json)
2. V portálu pro správu Azure zásobníku, vyberte **nový > nasazení šablony** k nasazení z vlastní šablony.
3. Vyberte **úpravy šablony** upravit nové vlastní šablony.
4. V podokně úprav na pravé straně zkopírujte a vložte vedoucí šablony JSON, které jste dříve stáhli.
    
    ![Úprava šablony vedoucí](media/azure-stack-ethereum/edit-leader-template.png)

5. Vyberte **Uložit**.
6. Vyberte **upravit parametry** a dokončete parametry šablony pro vaše nasazení.
    
    ![Upravit parametry šablony vedoucí](media/azure-stack-ethereum/edit-leader-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Řetězec se používá jako základ pro pojmenování nasazené prostředky. | Alfanumerické znaky o délce 1 až 6 | eth
    AUTHTYPE | Metoda ověřování do virtuálního počítače. | Heslo nebo SSH veřejný klíč | Heslo
    ADMINUSERNAME | Uživatelské jméno správce každé nasazení virtuálních počítačů | 1 - 64 znaků. | gethadmin
    ADMINPASSWORD (typ ověřování = heslo)| Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat 3 z následujících požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslice a 1 speciální znak. <br />I když všechny virtuální počítače původně má stejné heslo, můžete změnit heslo po zřízení.|12 - 72 znaků|
    ADMINSSHKEY (typ ověřování = parametru sshPublicKey) | Klíč zabezpečeného prostředí použít pro vzdálené přihlášení. | |
    GENESISBLOCK | Řetězce formátu JSON představující vlastní genesis bloku. | |
    ETHEREUMACCOUNTPSSWD | Heslo správce používá k zabezpečení účtu Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | Přístupové heslo používané ke generování privátního klíče, které jsou přidružené k účtu Ethereum. | |
    ETHEREUMNETWORKID | ID sítě konsorcium. | Použít libovolnou hodnotu od 5 do 999 999 999 | 72
    CONSORTIUMMEMBERID | ID spojené s každý člen consortium sítě.   | Toto ID musí být jedinečné v síti. | 0
    NUMMININGNODES | Počet uzlů dolování. | Mezi 2 a 15. | 2
    MNNODEVMSIZE | Velikost virtuálního počítače uzlů dolování. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Výkon úložiště uzlů dolování. | | Standard_LRS
    NUMTXNODES | Počet uzlů transakce. | Od 1 do 5. | 1
    TXNODEVMSIZE | Velikost virtuálního počítače uzlů transakce. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Výkon úložiště uzlů transakce. | | Standard_LRS
    BASEURL | Základní adresa URL pro získání podle šablon z. | Pokud chcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. V **vlastní nasazení**, zadejte **předplatné**, **skupiny prostředků**, a **umístění skupiny prostředků**.
    
    ![Vedoucí nasazení parametry](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Předplatné, do které chcete nasadit consortium sítě | | Spotřeba předplatného
    Skupina prostředků | Skupinu prostředků, do které chcete nasadit consortium sítě. | | EthereumResources
    Umístění | Oblast Azure pro skupinu prostředků. | | místní

8. Vyberte **Vytvořit**.

Nasazení může trvat 20 minut nebo déle.

Po dokončení nasazení můžete zkontrolovat souhrnné pro nasazení **společnosti Microsoft. Šablona** v části nasazení skupiny prostředků. Souhrn obsahuje výstupní hodnoty, které lze použít pro připojení consortium členy.

Pokud chcete ověřit vedoucí nasazení, procházejte vedoucí – web pro správu. Adresa správce lokality najdete v části výstup **Microsoft.Template** nasazení.  

![Vedoucí nasazení souhrn](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Připojení consortium člen nasazení

1. Stažení [consortium člen šablony z Githubu](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/JoiningMember/mainTemplate.json)
2. V portálu pro správu Azure zásobníku, vyberte **nový > nasazení šablony** k nasazení z vlastní šablony.
3. Vyberte **úpravy šablony** upravit nové vlastní šablony.
4. V podokně úprav na pravé straně zkopírujte a vložte vedoucí šablony JSON, které jste dříve stáhli.
5. Vyberte **Uložit**.
6. Vyberte **upravit parametry** a dokončete parametry šablony pro vaše nasazení.

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Řetězec se používá jako základ pro pojmenování nasazené prostředky. | Alfanumerické znaky o délce 1 až 6 | eth
    AUTHTYPE | Metoda ověřování do virtuálního počítače. | Heslo nebo SSH veřejný klíč | Heslo
    ADMINUSERNAME | Uživatelské jméno správce každé nasazení virtuálních počítačů | 1 - 64 znaků. | gethadmin
    ADMINPASSWORD (typ ověřování = heslo)| Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat 3 z následujících požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslice a 1 speciální znak. <br />I když všechny virtuální počítače původně má stejné heslo, můžete změnit heslo po zřízení.|12 - 72 znaků|
    ADMINSSHKEY (typ ověřování = parametru sshPublicKey) | Klíč zabezpečeného prostředí použít pro vzdálené přihlášení. | |
    CONSORTIUMMEMBERID | ID spojené s každý člen consortium sítě.   | Toto ID musí být jedinečné v síti. | 0
    NUMMININGNODES | Počet uzlů dolování. | Mezi 2 a 15. | 2
    MNNODEVMSIZE | Velikost virtuálního počítače uzlů dolování. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Výkon úložiště uzlů dolování. | | Standard_LRS
    NUMTXNODES | Počet uzlů transakce. | Od 1 do 5. | 1
    TXNODEVMSIZE | Velikost virtuálního počítače uzlů transakce. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Výkon úložiště uzlů transakce. | | Standard_LRS
    CONSORTIUMDATA | Adresa URL odkazující na konfigurační data relevantní consortium poskytované jiného člena nasazení. Tuto hodnotu najdete na vedoucí nasazení výstup. | |
    REMOTEMEMBERVNETADDRESSSPACE | Hodnocení chyb zabezpečení IP adresa vedoucí. Tuto hodnotu najdete na vedoucí nasazení výstup. | | 
    REMOTEMEMBERNVAPUBLICIP | Hodnocení chyb zabezpečení IP adresa vedoucí. Tuto hodnotu najdete na vedoucí nasazení výstup. | | 
    CONNECTIONSHAREDKEY | Předem zavedené tajný klíč mezi členy consortium sítě, které jsou navázání připojení. | |
    BASEURL | Základní adresu URL pro šablonu. | Pokud chcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. V **vlastní nasazení**, zadejte **předplatné**, **skupiny prostředků**, a **umístění skupiny prostředků**.

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Předplatné, do které chcete nasadit consortium sítě | | Spotřeba předplatného
    Skupina prostředků | Skupinu prostředků, do které chcete nasadit consortium sítě. | | MemberResources
    Umístění | Oblast Azure pro skupinu prostředků. | | místní

8. Vyberte **Vytvořit**.

Nasazení může trvat 20 minut nebo déle.

Po dokončení nasazení můžete zkontrolovat souhrnné pro nasazení **Microsoft.Template** v části nasazení skupiny prostředků. Souhrn obsahuje výstupní hodnoty, které lze použít pro připojení consortium členy.

K ověření nasazení člena, procházejte web pro správu člena. Adresa správce lokality najdete v části výstup Microsoft.Template nasazení.

![Člen nasazení souhrn](media/azure-stack-ethereum/ethereum-node-status-2.png)

Jak je znázorněno na obrázku, je stav uzlů člena **neběží**. To je proto nebude navázáno připojení mezi členem a vedoucí. Připojení mezi členem a vedoucí je obousměrného připojení. Když nasadíte člen, šablony automaticky vytvoří připojení od člena na vedoucí. Chcete-li vytvořit připojení z vedoucí k člen přejděte k dalšímu kroku.

### <a name="connect-member-and-leader"></a>Připojit člen a vedoucí

Tato šablona vytvoří připojení z vedoucí na vzdálené člena. 

1. Stažení [připojit člen a vedoucí šablony z Githubu](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/Connection/mainTemplate.json)
2. V portálu pro správu Azure zásobníku, vyberte **nový > nasazení šablony** k nasazení z vlastní šablony.
3. Vyberte **úpravy šablony** upravit nové vlastní šablony.
4. V podokně úprav na pravé straně zkopírujte a vložte vedoucí šablony JSON, které jste dříve stáhli.
    
    ![Upravit připojení šablony](media/azure-stack-ethereum/edit-connect-template.png)

5. Vyberte **Uložit**.
6. Vyberte **upravit parametry** a dokončete parametry šablony pro vaše nasazení.
    
    ![Upravit připojení parametry šablony](media/azure-stack-ethereum/edit-connect-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Předpona názvu vedoucí. Tuto hodnotu najdete na vedoucí nasazení výstup.  | Alfanumerické znaky o délce 1 až 6 | |
    MEMBERROUTETABLENAME | Název vedoucí směrovací tabulku. Tuto hodnotu najdete na vedoucí nasazení výstup. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Adresní prostor člena. Tuto hodnotu najdete na výstup nasazení člena. | |
    CONNECTIONSHAREDKEY | Předem zavedené tajný klíč mezi členy consortium sítě, které jsou navázání připojení.  | |
    REMOTEMEMBERNVAPUBLICIP | Hodnocení chyb zabezpečení IP adresa tohoto člena. Tuto hodnotu najdete na výstup nasazení člena. | |
    MEMBERNVAPRIVATEIP | Vedoucí privátní hodnocení chyb zabezpečení IP adresu. Tuto hodnotu najdete na vedoucí nasazení výstup. | |
    UMÍSTĚNÍ | Umístění prostředí Azure zásobníku. | | místní
    BASEURL | Základní adresu URL pro šablonu. | Pokud chcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. V **vlastní nasazení**, zadejte **předplatné**, **skupiny prostředků**, a **umístění skupiny prostředků**.
    
    ![Připojit parametry nasazení](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Vedoucí předplatné. | | Spotřeba předplatného
    Skupina prostředků | Vedoucí skupiny prostředků. | | EthereumResources
    Umístění | Oblast Azure pro skupinu prostředků. | | místní

8. Vyberte **Vytvořit**.

Po dokončení nasazení trvá několik minut pro vedoucího a člen komunikaci. Pro ověření nasazení, aktualizujte člena – web pro správu. Stav uzlů člen by měl být spuštěn. 

![Ověření nasazení](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Další postup

- Další informace o Ethereum a Azure, najdete v části [Blockchain technologie a aplikací | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Další informace o scénářích blockchain v Azure najdete v tématu [šablona řešení ověření pracovní consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).