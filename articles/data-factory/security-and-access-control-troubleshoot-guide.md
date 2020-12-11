---
title: Řešení potíží se zabezpečením a řízením přístupu
description: Naučte se řešit problémy zabezpečení a řízení přístupu v Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109741"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Řešení potíží s Azure Data Factory zabezpečení a řízení přístupu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží při zabezpečení a řízení přístupu v Azure Data Factory.

## <a name="common-errors-and-messages"></a>Běžné chyby a zprávy


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>Problém s připojením aktivity kopírování v cloudovém úložišti dat

#### <a name="symptoms"></a>Příznaky

Pokud došlo k potížím s připojením ke zdroji nebo úložišti dat jímky, může se vrátit různé druhy chybových zpráv.

#### <a name="cause"></a>Příčina 

Problém je většinou způsoben následujícími faktory:

1. Nastavení proxy serveru v místním prostředí IR (Pokud používáte prostředí IR pro místní hostování)

1. Nastavení brány firewall v uzlu IR v místním prostředí (Pokud používáte technologii IR pro místní hostování)

1. Nastavení brány firewall v cloudovém úložišti dat

#### <a name="resolution"></a>Řešení

1. Nejprve zkontrolujte následující body, abyste se ujistili, že problém způsobuje problémy s připojením:

   - Chyba je vyvolána z konektorů zdroje nebo jímky.

   - Aktivita na začátku kopie se nezdařila.

   - Jedná se o konzistentní selhání pro Azure IR nebo v místním prostředí IR s jedním uzlem, protože se může jednat o náhodnou chybu pro prostředí IR s více uzly, pokud problém obsahuje jenom část uzlů.

1. Ověřte, zda se jedná o nastavení proxy serveru, brány firewall a sítě, pokud používáte prostředí **IR** v místním prostředí, protože spuštění do stejného úložiště dat může být v Azure IR úspěšné. Řešení potíží najdete na následujících odkazech:

   [Porty IR a brány firewall](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    pro místní hostování [Konektor adls](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. Pokud používáte **Azure IR**, zkuste prosím zakázat nastavení brány firewall úložiště dat. Tímto způsobem lze opravit problém následujících dvou okolností:
  
   * [Azure IR IP adresy](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) nejsou v seznamu povolených.

   * *Povolit důvěryhodným službám Microsoftu přístup k této funkci účtu úložiště* je pro [Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) a [adls Gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities)vypnuté.

   * *Povolení přístupu ke službám Azure* není pro adls Gen1 povolené.

1. Pokud výše uvedené metody nefungují, obraťte se prosím na Microsoft, abychom vám pomohli.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Neplatný nebo prázdný problém ověřovacího klíče po zakázání přístupu k veřejné síti

#### <a name="symptoms"></a>Příznaky

Po zakázání přístupu k veřejné síti pro Data Factory, protože modul runtime integrace v místním prostředí vyvolá následující chybu: "ověřovací klíč je neplatný nebo prázdný."

#### <a name="cause"></a>Příčina

Problém je pravděpodobně způsoben problémem překladu názvů DNS, protože zakázání veřejného připojení a vytvoření privátního koncového bodu nemá k dispozici žádnou nápovědu pro opětovné připojení.

Pokud chcete ověřit, jestli se Data Factory plně kvalifikovaný název domény přeloží na veřejnou IP adresu, můžete postupovat podle následujících kroků:

1. Potvrďte, že jste virtuální počítač Azure vytvořili ve stejné virtuální síti jako Data Factory privátní koncový bod.

2. Spusťte PsPing a proveďte příkaz k odeslání z virtuálního počítače Azure do Data Factory plně kvalifikovaný název domény:

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > Pro příkaz PsPing je nutné zadat port, zatímco port 443 není nutné.

3. Ověřte, zda jsou oba příkazy přeloženy na veřejnou IP adresu ADF, která je založena na zadané oblasti (Format xxx. xxx. xxx. 0).

#### <a name="resolution"></a>Řešení

- Informace o Azure Data Factory najdete v článku věnovaném [soukromému odkazu na Azure](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints). Instrukce je určena pro konfiguraci privátní zóny DNS/serveru pro překládání Data Factory plně kvalifikovaného názvu domény na privátní IP adresu.

- Pokud nejste ochotni nakonfigurovat soukromou zónu DNS/server aktuálně, proveďte prosím jako dočasné řešení níže uvedené kroky. Vlastní DNS se ale pořád doporučuje jako dlouhodobé řešení.

  1. Změňte soubor hostitele v systému Windows a namapujte privátního koncového bodu (ADF) privátního protokolu IP na ADF plně kvalifikovaný název domény.
  
     Přejděte na cestu "C:\Windows\System32\drivers\etc" na virtuálním počítači Azure a otevřete soubor **hostitele** pomocí poznámkového bloku. Na konec souboru přidejte řádek mapování privátních IP adres na plně kvalifikovaný název domény a uložte změnu.
     
     ![Přidat mapování na hostitele](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Spusťte stejné příkazy v části výše ověřovací postup a ověřte odpověď, která by měla obsahovat privátní IP adresu.

  1. Proveďte znovu registraci místního prostředí Integration runtime a problém by se měl vyřešit.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nepovedlo se zaregistrovat ověřovací klíč IR na virtuálních počítačích s místním hostováním z důvodu privátního propojení.

#### <a name="symptoms"></a>Příznaky

Nepovedlo se zaregistrovat ověřovací klíč IR na virtuálním počítači s místním hostováním, protože je povolené privátní propojení.

Informace o chybě jsou uvedené níže:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Příčina

Důvodem může být to, že virtuální počítač, ve kterém se pokoušíte nainstalovat prostředí IR v místním prostředí, je problém. Pokud se chcete připojit ke cloudu, měli byste se ujistit, že je povolený přístup k veřejné síti.

#### <a name="resolution"></a>Řešení

 **Řešení 1:** Problém můžete vyřešit pomocí následujících kroků:

1. Přejít na stránku [továrny – aktualizace](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) .

1. V pravém horním rohu vyberte tlačítko **vyzkoušet** .

1. V části **parametry** vyplňte požadované informace. 

1. Do pole **tělo** vložte následující vlastnost:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Pokud chcete funkci spustit, vyberte **Spustit** . 

1. Potvrďte, že se zobrazuje **kód odpovědi: 200** . Vložená vlastnost by měla být také zobrazená v definici JSON.

1. Do prostředí Integration runtime znovu přidejte ověřovací klíč IR.


**Řešení 2:** K řešení můžete použít následující článek:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Zkuste povolit přístup k veřejné síti v uživatelském rozhraní, jak je znázorněno na následujícím snímku obrazovky:

![Povolit přístup k veřejné síti](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Privátní odkaz pro Data Factory](data-factory-private-link.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
