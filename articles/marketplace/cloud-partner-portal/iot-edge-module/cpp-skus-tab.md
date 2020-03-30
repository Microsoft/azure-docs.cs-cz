---
title: SKU modulu Azure IoT Edge | Azure Marketplace
description: Vytvořte skupro modul IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: dsindona
ms.openlocfilehash: 49ae69263735d6ec35cd911a20100472b32c0b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286553"
---
# <a name="iot-edge-module-skus-tab"></a>Karta SKU modulu IoT Edge

Karta **SKU** na stránce **Nová nabídka** umožňuje vytvořit jednu nebo více slok a přidružit je k nové nabídce.  Můžete použít různé sku sku rozlišovat řešení podle sad funkcí, fakturační modely nebo některé jiné charakteristiky.


## <a name="sku-settings"></a>Nastavení skladové položky

Když začnete vytvářet novou nabídku, nejsou k nabídce přidruženy žádné souspolečnosti. Chcete-li vytvořit novou skladovou položku, postupujte takto:

- Na stránce **Moduly IoT Edge > Nová nabídka** vyberte kartu **SKU.**
- V části Skladové položky vyberte **+ Nová skladová položka,** chcete-li otevřít dialogové okno.

  ![Nové tlačítko Skladové položky na kartě Nová nabídka pro moduly IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- V dialogovém okně **Nová skladová položka** zadejte identifikátor skladové položky a pak vyberte **OK**.
(V následující tabulce jsou uvedeny konvence pojmenování identifikátorů.)

Karta **Skladové položky** se aktualizuje a zobrazí pole, která upravujete pro konfiguraci skladové položky. Hvězdička (*) připojená k názvu pole označuje, že je požadováno.

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID skladové položky\***       | Identifikátor této skladové položky. Tento název má maximálně 50 znaků, skládající se z malých alfanumerických znaků nebo pomlčky (-), ale nemůže končit pomlčkou. **Poznámka:** Po publikování nabídky nelze tento název změnit. Název je veřejně viditelný v adresách URL produktu. |
|  |  |


## <a name="sku-details"></a>Podrobnosti o skladové jednotce

Nakonfigurujte **podrobnosti skladové položky** a definujte, jak se vaše skladová položka zobrazí na webech Azure Marketplace a Azure Portal.

![Metadata sku modulu IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

Následující tabulka popisuje účel, obsah a formátování polí v části **Podrobnosti skladové položky**. Povinná pole jsou obžalována hvězdičkou (*).

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **Název\***        | Název této skladové položky. Maximální délka 50 znaků. <br/> Zobrazí se na webu Azure Portal a bude použit jako výchozí název modulu (bez mezer a speciálních znaků), když se nasadí. Podívejte se na obrázky níže a přesně uvidíte, kde je toto pole zobrazeno.|
| **Souhrn\***      | Krátké shrnutí této skladové položky. Maximální délka 100 znaků. **Neshrnujte** nabídku, jen skladovou položku.  Tento souhrn se zobrazí na Azure Marketplace. Podívejte se na obrázky níže a přesně uvidíte, kde je toto pole zobrazeno.|
| **Popis\***  | Stručný popis této skladové položky. Maximální délka 3000 znaků. Nepopisují nabídku, ale jen tento Skladové položky. Zobrazí se na azure marketplace a na webu Azure Portal. Na webu Azure Portal se připojí k popisu marketplace popisující nabídku definovanou na kartě Marketplace.  Může být stejný jako souhrn skladové položky. Podívejte se na obrázky níže a přesně uvidíte, kde je toto pole zobrazeno.|
| **Skrýt tuto skladovou položku\*** | Zachovat výchozí nastavení, které je **Ne**. |
|  |  |


### <a name="sku-example"></a>Příklad skladové položky

 Následující příklady ukazují, jak se pole **Název**skladové položky , **Souhrn**a **Popis** zobrazují v různých zobrazeních.
 

#### <a name="on-the-azure-marketplace-website"></a>Na webu Azure Marketplace:

- Při pohledu na podrobnosti sku:

    ![Jak se na webu Azure Marketplace zobrazují skutonové sady](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Na webu Portálu Azure:

- Při procházení sku:

    ![Jak se při procházení portálu Azure #1 zobrazí modul IoT Edge](media/iot-edge-module-portal-browse.png)

    ![Jak se při procházení portálu Azure #2](media/iot-edge-module-portal-product-picker.png)

- Při hledání skutých a konečníku:

    ![Jak se při prohledávání webu Azure Portal zobrazuje modul IoT Edge](media/iot-edge-module-portal-search.png)

- Při pohledu na podrobnosti sku:

    ![Jak se modul IoT Edge zobrazuje při hledání podrobností o produktu na portálu](./media/iot-edge-module-portal-pdp.png)

- Při nasazování modulu:
    
    ![Jak se při nasazování zobrazuje modul IoT Edge](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Obsah skladové položky

V části **Obrázky edge modulu**poskytněte informace, které potřebujeme k nahrání modulu IoT Edge.

Ubytujte nám přístup k [vašemu registru kontejnerů Azure](https://azure.microsoft.com/services/container-registry/) (ACR), který obsahuje bitovou kopii modulu IoT Edge, abychom ho mohli nahrát a certifikovat. Po publikování se váš modul IoT Edge zkopíruje a distribuuje pomocí registru veřejných kontejnerů hostovaného na Azure Marketplace.

Můžete cílit na více platforem a poskytnout několik verzí prostřednictvím značek. Další informace o [značkách a správě verzí najdete v technických prostředcích modulu "Připravte si modul IoT Edge"](./cpp-create-technical-assets.md).

![Obrázky modulu IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

Následující tabulka popisuje účel, obsah a formátování polí pro **oddíly Podrobnosti úložiště obrázků** a **Verze obrázku**.  Povinná pole jsou obžalována hvězdičkou (*).


|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Podrobnosti o úložišti obrázků***    |
| **ID předplatného\***        | ID předplatného Azure vašeho ACR.|
| **Název skupiny prostředků\***      | Název skupiny prostředků acr.|
| **Název registru\***  | Název registru ACR. Zkopírujte pouze název registru, nikoli název přihlašovacího `azurecr.io`serveru (například bez .) |
| **Název úložiště\***  | Název úložiště acr, který obsahuje modul IoT Edge. **Poznámka:** Po nastavení názvu jej nelze později změnit. Použijte jedinečný název, abyste zajistili, že žádná jiná nabídka ve vašem účtu nebude mít stejný název. |
| **Username\*** | Uživatelské jméno přidružené k vašemu ACR (uživatelské jméno správce). |
| **Heslo\*** | Heslo přidružené k acr. |
|    |  ***Verze image***   |
| **Značka obrázku nebo Digest\*** | Musí obsahovat alespoň `latest` značku a značku verze (například počínaje místem, `xx.xx.xx-` kde xx je číslo). Měly by být [značky manifestu,](https://github.com/estesp/manifest-tool) které cílí na více platforem. Všechny značky odkazované značkou manifestu musí být také přidány, abychom je mohli nahrát. Pomocí značek můžete přidat několik verzí modulu IoT Edge. Všechny značky `latest`manifestu (kromě) `X.Y.Z-` musí začínat buď buď `X.Y-` s celými písmeny X, Y, Z. Další informace o [značkách a správě verzí najdete v technických prostředcích modulu "Připravte si modul IoT Edge"](./cpp-create-technical-assets.md). <br/> Pokud například `latest` značka odkazuje na `1.0.1-linux-x64`tuto `1.0.1-linux-arm32`značku `1.0.1-windows-arm32`, je třeba přidat těchto 6 značek. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Pomozte zákazníkům spustit pomocí výchozího nastavení

Definujte nejběžnější nastavení pro nasazení modulu IoT Edge. Optimalizujte nasazení zákazníků tím, že jim umožníte spustit modul IoT Edge out-of-the-box s těmito výchozími hodnotami.

![Výchozí nastavení modulu IoT Edge při nasazení](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

Následující tabulka popisuje účel, obsah a formátování polí pro **výchozí trasy**, Výchozí **vlastnosti požadované dvojčete**, **Výchozí proměnné prostředí**a **Výchozí možnosti vytvoření**.

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **Výchozí trasy**        | Každý výchozí název a hodnota postupu musí být menší než 512 znaků. Můžete definovat až 5 výchozích tras. Ujistěte se, že v hodnotě trasy používáte správnou [syntaxi trasy.](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) Chcete-li odkazovat na modul, použijte jeho výchozí název modulu, který bude název **skladové položky** bez mezer a speciálních znaků. Chcete-li odkazovat na další moduly, které ještě nejsou známy, použijte `<FROM_MODULE_NAME>` konvenci, aby zákazníci věděli, že tyto informace potřebují aktualizovat. Další informace o [trasách IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Pokud například `ContosoModule` modul naslouchá vstupům `ContosoInput` na a `ContosoOutput`výstupním datům na , má smysl definovat následující 2 výchozí trasy:<br/>- název #1:`ToContosoModule`<br/>- Hodnota #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Název #2:`FromContosoModuleToCloud`<br/>- Hodnota #2:`FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Výchozí požadované vlastnosti dvojčete**      | Každý výchozí název a hodnota požadovaných vlastností dvojčete musí být menší než 512 znaků. Můžete definovat až 5 požadovaných vlastností dvojčete názvu/hodnoty. Hodnoty twin požadované vlastnosti musí být platné JSON, non-uvozené, bez polí a s maximální vnořené hierarchie 4. Další informace o [dvoupožadovaných vlastnostech](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Pokud například modul podporuje dynamicky konfigurovatelnou obnovovací frekvenci prostřednictvím požadovaných vlastností dvojčete, má smysl definovat následující výchozí požadovanou vlastnost dvojčete:<br/> - název #1:`RefreshRate`<br/>- Hodnota #1:`60`|
| **Výchozí proměnné prostředí**  | Každý výchozí název a hodnota proměnných prostředí musí být menší než 512 znaků. Můžete definovat až 5 proměnných prostředí název/hodnota. <br/>Pokud například modul vyžaduje přijetí podmínek použití před spuštěním, můžete definovat následující proměnnou prostředí:<br/> - název #1:`ACCEPT_EULA`<br/>- Hodnota #1:`Y`|
| **Výchozí možnosti vytvoření**  | CreateOptions musí být menší než 512 znaků. Musí to být platný JSON, neuvozený. Další informace o [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Například pokud modul vyžaduje vazbu port, můžete definovat následující createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Vyberte **Uložit,** chcete-li uložit nastavení skladové položky. 


## <a name="next-steps"></a>Další kroky

Na [kartě Marketplace](./cpp-marketplace-tab.md) můžete vytvořit popis marketplace pro vaši nabídku.
