---
title: SKU modulu Azure IoT Edge | Azure Marketplace
description: Vytvořte SKU pro modul IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 230f3d6438d44c4e1e1721c0cb1453c85958e282
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813851"
---
# <a name="iot-edge-module-skus-tab"></a>Karta SKU modulu IoT Edge

Karta **SKU** na nové stránce **nabídky** umožňuje vytvořit jednu nebo více SKU a přidružit je k nové nabídce.  Pomocí různých SKU můžete odlišit řešení pomocí sad funkcí, modelů fakturace nebo některé jiné charakteristiky.


## <a name="sku-settings"></a>Nastavení SKU

Když začnete vytvářet novou nabídku, nejsou k této nabídce přidružené žádné skladové položky. Chcete-li vytvořit novou SKLADOVOU položku, postupujte takto:

- Na stránce **IoT Edge moduly > nové nabídky** vyberte kartu **SKU** .
- V části SKU vyberte **+ Nová SKU** a otevřete dialogové okno.

  ![Tlačítko Nová SKU na nové kartě Nabídka pro IoT Edge moduly](./media/iot-edge-module-skus-tab-new-sku.png)

- V dialogovém okně **Nová SKU** zadejte identifikátor SKU a pak vyberte **OK**.
(Následující tabulka poskytuje konvence pojmenování identifikátorů.)

Karta **SKU** se aktualizuje a zobrazí pole, která upravíte pro konfiguraci SKU. Hvězdička (*), která je připojena k názvu pole, označuje, že je požadováno.

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID SKU\***       | Identifikátor této SKU Tento název má maximálně 50 znaků, který se skládá z malých alfanumerických znaků nebo pomlček (-), ale nemůže končit pomlčkou. **Poznámka:** Po publikování nabídky nelze tento název změnit. Název je veřejně viditelný v adresách URL produktu. |
|  |  |


## <a name="sku-details"></a>Podrobnosti SKU

Nakonfigurujte **Podrobnosti SKU** , abyste definovali, jak se vaše skladová jednotka zobrazí na webech portálu Azure Marketplace a na webu Azure Portal.

![Metadata SKU modulu IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

Následující tabulka popisuje účel, obsah a formátování polí v **podrobnostech SKU**. Požadovaná pole jsou indicted hvězdičkou (*).

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **\* názvu**        | Název této SKU Maximální délka 50 znaků. <br/> Bude se zobrazovat na webu Azure Portal a bude se používat jako výchozí název modulu (bez mezer a speciálních znaků) při jeho nasazení. Chcete-li zobrazit přesně, kde se toto pole zobrazuje, podívejte se na obrázky níže.|
| **Souhrn\***      | Krátký souhrn této SKU Maximální délka 100 znaků. Nevytvářejte **Souhrn nabídky** , stačí pouze SKU.  Tento souhrn se zobrazí v Azure Marketplace. Chcete-li zobrazit přesně, kde se toto pole zobrazuje, podívejte se na obrázky níže.|
| **Popis\***  | Krátký popis této SKU Maximální délka 3000 znaků. Nepopisujte nabídku, ale pouze tuto skladovou jednotku. Bude se zobrazovat na webu Azure Marketplace a v Azure Portal. V Azure Portal se připojí k popisu na webu Marketplace, který popisuje nabídku definovanou na kartě Marketplace.  Může to být stejné jako souhrn SKU. Chcete-li zobrazit přesně, kde se toto pole zobrazuje, podívejte se na obrázky níže.|
| **Skrýt tento\* SKU** | Nechejte výchozí nastavení, což **není.** |
|  |  |


### <a name="sku-example"></a>Příklad SKU

 Následující příklady ukazují, jak se pole **název**SKU, **Souhrn**a **Popis** zobrazují v různých zobrazeních.
 

#### <a name="on-the-azure-marketplace-website"></a>Na webu Azure Marketplace:

- Při prohlížení podrobností SKU:

    ![Jak se zobrazují skladové jednotky na Azure Marketplace webu](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Na webu Azure Portal:

- Při procházení SKU:

    ![Jak se IoT Edge modul zobrazuje při procházení Azure Portal #1](media/iot-edge-module-portal-browse.png)

    ![Jak se IoT Edge modul zobrazuje při procházení Azure Portal #2](media/iot-edge-module-portal-product-picker.png)

- Při hledání SKU:

    ![Jak se IoT Edge modul zobrazuje při hledání Azure Portal](media/iot-edge-module-portal-search.png)

- Při prohlížení podrobností SKU:

    ![Jak se IoT Edge modul zobrazuje při prohlížení podrobností o produktu na portálu](./media/iot-edge-module-portal-pdp.png)

- Při nasazování modulu:
    
    ![Jak se IoT Edge modul zobrazuje při nasazení](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Obsah SKU

V části **Image modulu Edge**zadejte informace, které potřebujeme k nahrání IoT Edgeho modulu.

Poskytněte nám přístup k vašemu [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR), který obsahuje váš obrázek IoT Edge modulu, abychom ho mohli nahrát a certifikovat. Po publikování se váš modul IoT Edge zkopíruje a distribuuje pomocí veřejného registru kontejneru hostovaného Azure Marketplace.

Můžete cílit na více platforem a poskytnout několik verzí prostřednictvím značek. Přečtěte si další informace o [značkách a verzích v tématu "Příprava vašich technických prostředků IoT Edge modulu"](./cpp-create-technical-assets.md).

![Image modulu IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

Následující tabulka popisuje účel, obsah a formátování polí pro **informace o úložišti** imagí oddílů a **verzi image**.  Požadovaná pole jsou indicted hvězdičkou (*).


|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Podrobnosti o úložišti imagí***    |
| **ID předplatného\***        | ID předplatného Azure vašeho ACRu.|
| **Název skupiny prostředků\***      | Název skupiny prostředků vašeho ACRu.|
| **Název registru\***  | Název registru ACR Pouze zkopírujte název registru, nikoli název přihlašovacího serveru (například bez `azurecr.io`.) |
| **Název úložiště\***  | Název úložiště ACR, který obsahuje váš modul IoT Edge. **Poznámka:** Po nastavení je název nelze později změnit. Použijte jedinečný název, abyste se ujistili, že žádné jiné nabídky ve vašem účtu nemají stejný název. |
| **Uživatelské jméno\*** | Uživatelské jméno přidružené k vašemu ACR (uživatelské jméno správce). |
| **\* hesla** | Heslo přidružené k vašemu ACR. |
|    |  ***Verze image***   |
| **Značka obrázku nebo\* Digest** | Musí obsahovat alespoň značku `latest` a značku verze (například počínaje `xx.xx.xx-`, kde XX je číslo). Měly by být v [manifestu značky](https://github.com/estesp/manifest-tool) , aby bylo možné cílit na více platforem. Všechny značky, na které se odkazuje značka manifestu, musí být také přidané, aby je bylo možné nahrát. Pomocí značek můžete přidat několik verzí modulu IoT Edge. Všechny značky manifestu (s výjimkou `latest`) musí začínat buď `X.Y-`, nebo `X.Y.Z-`, kde X, Y, Z jsou celá čísla. Přečtěte si další informace o [značkách a verzích v tématu "Příprava vašich technických prostředků IoT Edge modulu"](./cpp-create-technical-assets.md). <br/> Pokud například značka `latest` odkazuje na tyto body na `1.0.1-linux-x64`, `1.0.1-linux-arm32`, a `1.0.1-windows-arm32`, je nutné přidat tyto 6 značek sem. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Pomáhat zákazníkům při spouštění pomocí výchozích nastavení

Zadejte nejběžnější nastavení pro nasazení modulu IoT Edge. Optimalizujte zákaznická nasazení tím, že jim umožníte spustit modul IoT Edge předem s těmito výchozími hodnotami.

![Výchozí nastavení IoT Edge modulu v nasazení](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

Následující tabulka popisuje účel, obsah a formátování polí pro **výchozí trasy**, **výchozí hodnoty nevlákenných požadovaných vlastností**, **výchozí proměnné prostředí**a **výchozí createOptions**.

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **Výchozí trasy**        | U každého výchozího názvu a hodnoty trasy musí být méně než 512 znaků. Můžete definovat až 5 výchozích tras. V hodnotě trasy nezapomeňte použít správnou [syntaxi trasy](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) . Chcete-li se podívat na modul, použijte jeho výchozí název, který bude vaším názvem **SKU** bez mezer a speciálních znaků. Chcete-li se podívat na jiné moduly, které ještě nebyly známy, použijte `<FROM_MODULE_NAME>` konvence, aby vaši zákazníci věděli, že potřebují aktualizovat tyto informace. Přečtěte si další informace o [IoT Edgech trasách](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Pokud například modul `ContosoModule` naslouchá vstupům na `ContosoInput` a výstupní data v `ContosoOutput`, má smysl definovat následující 2 výchozí trasy:<br/>-Name #1: `ToContosoModule`<br/>-Value #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Name #2: `FromContosoModuleToCloud`<br/>-Value #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Výchozí požadované vlastnosti**      | U každého výchozího vlákna požadovaných vlastností musí být název a hodnota kratší než 512 znaků. Můžete definovat až 5 požadovaných vlastností s dvojitou hodnotou názvu nebo hodnoty. Hodnoty vlákenných požadovaných vlastností musí být platné JSON, bez řídicích znaků, bez polí a s maximální vnořenou hierarchií 4. Přečtěte si další informace o [dvojitě požadovaných vlastnostech](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Například pokud modul podporuje dynamickou konfigurovatelnou obnovovací frekvenci prostřednictvím nevlákenných požadovaných vlastností, má smysl definovat následující výchozí vlastnost s dvojitou hodnotou:<br/> -Name #1: `RefreshRate`<br/>-Value #1: `60`|
| **Výchozí proměnné prostředí**  | Všechny výchozí proměnné prostředí název a hodnota musí být kratší než 512 znaků. Můžete definovat až 5 proměnných prostředí s názvem a hodnotou. <br/>Například pokud modul vyžaduje, aby před zahájením přijímal podmínky použití, můžete definovat následující proměnnou prostředí:<br/> -Name #1: `ACCEPT_EULA`<br/>-Value #1: `Y`|
| **Výchozí createOptions**  | CreateOptions musí mít méně než 512 znaků. Musí být platný kód JSON, který neřídí řídicí znaky. Přečtěte si další informace o [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Například pokud modul vyžaduje vázání portu, můžete definovat následující createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Vyberte **Uložit** a uložte nastavení SKU. 


## <a name="next-steps"></a>Další kroky

Na [kartě Marketplace](./cpp-marketplace-tab.md) můžete vytvořit popis Marketplace pro vaši nabídku.
