---
title: Řešení potíží se zabezpečením a řízením přístupu
description: Naučte se řešit problémy zabezpečení a řízení přístupu v Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 37da2869f0cf315ac0a6851c9a77dcee4debf71a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581682"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Řešení potíží s Azure Data Factory zabezpečení a řízení přístupu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží při zabezpečení a řízení přístupu v Azure Data Factory.

## <a name="common-errors-and-messages"></a>Běžné chyby a zprávy

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Problém s připojením v aktivitě kopírování cloudového úložiště dat

#### <a name="symptoms"></a>Příznaky

Pokud dojde k problémům s připojením ve zdroji nebo úložišti dat jímky, můžou se vracet různé chybové zprávy.

#### <a name="cause"></a>Příčina 

Problém je obvykle způsoben jedním z následujících faktorů:

* Nastavení proxy v uzlu místního prostředí Integration runtime (IR), pokud používáte prostředí IR v místním prostředí.

* Nastavení brány firewall v uzlu IR v místním prostředí, pokud používáte prostředí IR s místním hostováním.

* Nastavení brány firewall v cloudovém úložišti dat.

#### <a name="resolution"></a>Řešení

* Pokud se chcete ujistit, že se jedná o problém s připojením, zkontrolujte následující body:

   - Chyba je vyvolána ze zdroje nebo konektory jímky.
   - Selhání je na začátku aktivity kopírování.
   - Selhání je konzistentní pro Azure IR nebo v místním prostředí IR s jedním uzlem, protože může to být náhodné selhání v místním prostředí IR s více uzly, pokud problém obsahuje jenom některé uzly.

* Pokud používáte místní prostředí **IR**, ověřte nastavení proxy serveru, brány firewall a sítě, protože připojení ke stejnému úložišti dat by mohlo být úspěšné, pokud používáte Azure IR. Řešení potíží s tímto scénářem najdete v těchto tématech:

   * [Porty IR a brány firewall pro místní hostování](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Konektor Azure Data Lake Storage](./connector-azure-data-lake-store.md)
  
* Pokud používáte **Azure IR**, zkuste zakázat nastavení brány firewall úložiště dat. Tento přístup může vyřešit problémy v následujících dvou situacích:
  
   * [Azure IR IP adresy](./azure-integration-runtime-ip-addresses.md) nejsou v seznamu povolených adres.
   * Možnost *Povolit důvěryhodné služby Microsoftu pro přístup k tomuto účtu úložiště* je vypnutá pro [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) a [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities).
   * Pro Azure Data Lake Storage Gen1 není povolené nastavení *Povolit přístup ke službám Azure* .

Pokud žádná z předchozích metod nefunguje, požádejte o nápovědu Microsoft.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Neplatný nebo prázdný problém ověřovacího klíče po zakázání přístupu k veřejné síti

#### <a name="symptoms"></a>Příznaky

Po zakázání přístupu k veřejné síti pro Data Factory vyvolá místní prostředí Integration runtime následující chybu: "ověřovací klíč není platný, nebo je prázdný."

#### <a name="cause"></a>Příčina

Příčinou problému je pravděpodobně problém s rozlišením DNS (Domain Name System), protože zakázání veřejného připojení a vytvoření privátního koncového bodu zabraňuje opětovnému připojení.

Chcete-li ověřit, zda je Data Factory plně kvalifikovaný název domény (FQDN) přeložen na veřejnou IP adresu, postupujte následovně:

1. Potvrďte, že jste virtuální počítač Azure vytvořili ve stejné virtuální síti jako Data Factory privátní koncový bod.

2. Spusťte PsPing a proveďte příkaz k odeslání z virtuálního počítače Azure do Data Factory plně kvalifikovaného názvu domény:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Pro příkaz PsPing je nutné zadat port. Zde je zobrazený port 443, ale není povinný.

3. Zkontrolujte, zda oba příkazy řeší Azure Data Factory veřejné IP adresy založené na zadané oblasti. IP adresa by měla být v následujícím formátu: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, postupujte takto:

- Jako možnost doporučujeme, abyste v rámci Data Factory "zóny DNS privátního propojení" přidali "Virtual Network odkaz". Podrobnosti najdete v článku věnovaném [privátním odkazům na Azure Azure Data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints) . Instrukce slouží ke konfiguraci privátní zóny DNS nebo vlastního serveru DNS k překladu Data Factory plně kvalifikovaného názvu domény na privátní IP adresu. 

- Pokud ale nechcete konfigurovat privátní zónu DNS nebo vlastní server DNS, zkuste následující dočasné řešení:

  1. Změňte soubor hostitele v systému Windows a namapujte privátní IP adresu (Azure Data Factory privátní koncový bod) na Azure Data Factory plně kvalifikovaný název domény.
  
     Na virtuálním počítači Azure přejít na `C:\Windows\System32\drivers\etc` a pak otevřete soubor *hostitele* v poznámkovém bloku. Přidejte řádek, který mapuje soukromou IP adresu na konec souboru, a uložte změnu.
     
     ![Snímek obrazovky s mapováním privátní IP adresy na hostitele](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Spusťte znovu stejné příkazy jako v předchozích krocích ověření a ověřte odpověď, která by měla obsahovat privátní IP adresu.

  1. Proveďte znovu registraci místního prostředí Integration runtime a problém by se měl vyřešit.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nepovedlo se zaregistrovat ověřovací klíč IR na virtuálních počítačích s místním hostováním z důvodu privátního propojení.

#### <a name="symptoms"></a>Příznaky

Nemůžete zaregistrovat ověřovací klíč IR na místním virtuálním počítači, protože je povolené privátní propojení. Zobrazí se následující chybová zpráva:

Nepovedlo se získat token služby ze služby ADF s klíčem * * * * * * * * * * * * * * * * * * * * * * a čas: 0,1250079 sekund, kód chyby: InvalidGatewayKey, activityId je: XXXXXXX a podrobná chybová zpráva je IP adresa klienta není platná. Služba Data Factory nedokázala získat přístup k veřejné síti, a proto se nemůže připojit ke cloudu, aby provedla úspěšné připojení. "

#### <a name="cause"></a>Příčina

Problém může být způsoben virtuálním počítačem, ve kterém se pokoušíte nainstalovat prostředí IR v místním prostředí. Pokud se chcete připojit ke cloudu, ujistěte se, že je povolený přístup k veřejné síti.

#### <a name="resolution"></a>Řešení

**Řešení 1**
 
Chcete-li tento problém vyřešit, postupujte takto:

1. Přejít na stránku [továrny – aktualizace](/rest/api/datafactory/Factories/Update) .

1. V pravém horním rohu vyberte tlačítko **vyzkoušet** .
1. V části **parametry** vyplňte požadované informace. 
1. Do pole **tělo** vložte následující vlastnost:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Pokud chcete funkci spustit, vyberte **Spustit** . 

1. V části **parametry** vyplňte požadované informace. 

1. Do pole **tělo** vložte následující vlastnost:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Pokud chcete funkci spustit, vyberte **Spustit** . 
1. Potvrďte, že se zobrazuje **kód odpovědi: 200** . Vložená vlastnost by měla být také zobrazená v definici JSON.

1. Do prostředí Integration runtime znovu přidejte ověřovací klíč IR.


**Řešení 2**

Problém vyřešíte tak, že přejdete do [privátního odkazu Azure pro Azure Data Factory](./data-factory-private-link.md).

Zkuste povolit přístup k veřejné síti v uživatelském rozhraní, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s povoleným ovládacím prvkem povolit přístup k veřejné síti v podokně síť](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Privátní odkaz pro Data Factory](data-factory-private-link.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)