---
title: Modul Azure IoT Edge skladové položky | Dokumentace Microsoftu
description: Vytvoření skladové položky pro modul IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: a216f3ddd89ad92dbbae617c51d5fc9c478c0c03
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430782"
---
# <a name="iot-edge-module-skus-tab"></a>Kartu SKU modul IoT Edge

**SKU** karty **nová nabídka** stránka umožňuje vytvořit jeden nebo více skladové položky a přidružovat je k nové nabídky.  Různé skladové položky můžete použít k rozlišení řešení sady funkcí, modely fakturace nebo některé typické.

## <a name="sku-settings"></a>Nastavení SKU

Když začnete vytvářet novou nabídku, nejsou k dispozici žádné skladové položky související s nabídkou. Pokud chcete vytvořit novou skladovou Položku, postupujte podle těchto kroků:

- Na **moduly IoT Edge > Nová nabídka** stránky, vyberte **SKU** kartu.
- V části skladové položky, vyberte **+ novou skladovou Položku** otevřete dialogové okno.

  ![Tlačítko Nová skladovou Položku na kartě nová nabídka pro moduly IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- Na **novou skladovou Položku** dialogového okna zadejte identifikátor pro danou skladovou jednotku a pak vyberte **OK**.
(Následující tabulka poskytuje zásady vytváření názvů identifikátor).

**SKU** kartu se aktualizují a zobrazí pole, které můžete upravovat konfiguraci SKU. Hvězdičku (*) připojeným k názvu pole označuje, že je to požadováno.

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU ID**       | Identifikátor pro toto SKU. Tento název může mít nejvýše 50 znaků, který se skládá z malé alfanumerické znaky nebo spojovníky (-), ale nemůže končit spojovníkem. **Poznámka:** po publikování nabídky nelze změnit tímto názvem. Název je veřejně viditelné v adresách URL produktu. |

## <a name="sku-details"></a>Podrobné údaje SKU

Konfigurace **podrobné údaje SKU** k definování, jak se zobrazí skladové jednotky na webech Azure Marketplace a webu Azure Portal.

![Metadata sku modulu IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

Následující tabulka popisuje účel, obsahu a formátování pro pole pod **podrobné údaje SKU**.

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **Název**        | Název pro tuto SKU. Maximální délka 50 znaků. <br/> Zobrazí se na webu Azure Portal a bude použit jako výchozí název modulu (bez mezer a zvláštních znaků) při nasazování. Zobrazit obrázky níže zobrazíte přesně kde se zobrazí toto pole.|
| **Souhrn**      | Krátký popis této skladové položky. Delší než 100 znaků. Proveďte **není** souhrn nabídky, stačí SKU.  Tento souhrn se zobrazí na webu Azure Marketplace. Zobrazit obrázky níže zobrazíte přesně kde se zobrazí toto pole.|
| **Popis**  | Krátký popis skladové jednotky. Maximální délka 3000 znaků. Nepopisují nabídky, ale právě tato skladová položka. Zobrazí se v azure marketplace a na webu Azure Portal. Na webu Azure Portal bude připojeno k Marketplace popis popisující nabídku definované na kartě Marketplace.  Může být stejný jako souhrn SKU. Zobrazit obrázky níže zobrazíte přesně kde se zobrazí toto pole.|
| **Skrýt toto SKU** | Zachovat výchozí nastavení, která je **ne**. |

### <a name="sku-example"></a>Příklad SKU

 Následující příklady ukazují jak SKU **Title**, **Souhrn**, a **popis** pole se zobrazí v zobrazení.
 
#### <a name="on-the-azure-marketplace-website"></a>Na webu Azure Marketplace:

- Při prohlížení podrobné údaje SKU:

    ![Zobrazení skladové položky na webu Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)

#### <a name="on-the-azure-portal-website"></a>Na webu Azure Portal:

- Při procházení skladové položky:

    ![Jak modul IoT Edge se zobrazí při procházení webu Azure portal #1](media/iot-edge-module-portal-browse.png)

    ![Jak modul IoT Edge se zobrazí při procházení webu Azure portal #2](media/iot-edge-module-portal-product-picker.png)

- Při hledání skladové položky:

    ![Jak modul IoT Edge se zobrazí při vyhledávání na webu Azure portal](media/iot-edge-module-portal-search.png)

- Při prohlížení podrobné údaje SKU:

    ![Jak modul IoT Edge se zobrazí při hledání podrobnosti o produktu na portálu](./media/iot-edge-module-portal-pdp.png)

- Při nasazování modul:
    
    ![Jak modul IoT Edge se zobrazí při nasazení](./media/iot-edge-module-deployment.png)

## <a name="sku-content"></a>Obsah skladové položky

V rámci bitové kopie modulu Edge poskytují informace, že budeme muset nahrát modul IoT Edge.

Uveďte přístup k vaší [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR), která obsahuje vaše IoT Edge modulu obrázku tak, aby jsme ho nahrát a potvrdit ho. Po publikování, modul IoT Edge se mají zkopírovat a distribuovat pomocí veřejného kontejneru registru hostovány na webu Azure Marketplace.

Můžete vyvíjet pro víc platforem a poskytují několik verzí pomocí značek. Další informace o [značky a správy verzí v "Příprava IoT Edge module technických prostředků"](./cpp-create-technical-assets.md).

![Bitové kopie modulu IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

Následující tabulka popisuje účel, obsah a formátování polí pro:

- **Podrobnosti úložiště obrázků**
- **Verze bitové kopie**

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
|  ***Podrobnosti úložiště obrázků***   |  |
| **ID předplatného**        | ID předplatného Azure z vaší služby ACR.|
| **Název skupiny prostředků**      | Název skupiny prostředků záznamu ACR.|
| **Název registru**  | Název vašeho registru ACR. Kopírovat pouze název registru, ne název přihlašovacího serveru (například bez `azurecr.io`.) |
| **Název úložiště**  | Název úložiště, který obsahuje modul IoT Edge záznamu ACR. **Poznámka:** po nastavení názvu nelze změnit později. Chcete-li zajistit, že v žádné jiné nabídky ve vašem účtu se stejným názvem, použijte jedinečný název. |
| **Uživatelské jméno** | Uživatelské jméno přidružené k záznamu ACR (uživatelské jméno správce). |
| **Heslo** | Heslo přidružené k záznamu ACR. |
|  ***Verze bitové kopie***   |  |
| **Značka obrázku nebo ověřování algoritmem Digest** | Musí zahrnovat alespoň `latest` značku a označení verze (například počínaje `xx.xx.xx-` kde xx je číslo). Měly by být [manifest značky](https://github.com/estesp/manifest-tool) pro více cílových platforem. Všechny značky odkazuje značku manifestu musí být rovněž přidána, takže jsme jejich nahrávání. Můžete přidat několik verzí modulu IoT Edge pomocí značek. Všechny značky manifest (s výjimkou `latest`) musí začínat buď `X.Y-` nebo `X.Y.Z-` kde X, Y, Z jsou celá čísla. Další informace o [značky a správy verzí v "Příprava IoT Edge module technických prostředků"](./cpp-create-technical-assets.md). <br/> Například pokud `latest` značky body, na kterou odkazuje `1.0.1-linux-x64`, `1.0.1-linux-arm32`,, a `1.0.1-windows-arm32`tato 6 značky potřeba do ní přidat tady. |

### <a name="help-your-customers-launch-your-iot-edge-module-by-using-default-settings"></a>Pomozte vašich zákazníkům spuštění pomocí výchozího nastavení modulu IoT Edge

Definujte nejběžnější nastavení pro nasazení modulu IoT Edge. Optimalizace nasazení zákazníků tím, že je spustí vaše IoT Edge modulu out-of-the-box s tyto výchozí hodnoty.

![Výchozí nastavení modulu IoT Edge v nasazení](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

Následující tabulka popisuje účel, obsah a formátování polí pro **trasy výchozí**, **požadované vlastnosti dvojčete výchozí**, **výchozí proměnné prostředí**, a **výchozí CreateOptions field**.

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **Výchozí trasy**        | Každý název výchozí trasy a hodnota musí být kratší než 512 znaků. Můžete definovat až 5 výchozí trasy. Ujistěte se, že používáte správné [trasy syntaxe](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) vaše hodnoty trasy. Odkazovat na modul, použijte výchozí název modulu, který bude vaše **název SKU** bez mezery a speciální znaky. K odkazování na ostatní moduly ještě není znám, použijte `<FROM_MODULE_NAME>` zásad tak, aby vaši zákazníci věděli, které potřebují k aktualizaci těchto informací. Další informace o [trasy IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Například pokud modul `ContosoModule` naslouchá vstupů `ContosoInput` a výstupní data v `ContosoOutput`, je vhodné definovat následující 2 výchozí trasy:<br/>-Název #1: `ToContosoModule`<br/>– Hodnota #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Název #2: `FromContosoModuleToCloud`<br/>– Hodnota #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOuput INTO $upstream`<br/>  |
| **Výchozí požadované vlastnosti dvojčete**      | Každý název požadované vlastnosti dvojčete výchozí a hodnota musí být kratší než 512 znaků. Můžete definovat až 5 názvu a hodnoty požadované vlastnosti dvojčete. Hodnoty požadované vlastnosti dvojčete musí být platný kód JSON, bez-uvozeny řídicími znaky, bez použití pole a maximální vnořené hierarchie 4. Další informace o [požadované vlastnosti dvojčete](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Například pokud modul podporuje dynamicky konfigurovat obnovovací frekvence prostřednictvím požadované vlastnosti dvojčete, je vhodné definovat následující výchozí vlastností dvojčat požadovaného:<br/> -Název #1: `RefreshRate`<br/>– Hodnota #1: `60`|
| **Výchozí proměnné prostředí**  | Každý název proměnné prostředí výchozí a hodnota musí být kratší než 512 znaků. Můžete definovat až 5 název/hodnota proměnné prostředí. <br/>Pokud modul vyžaduje přijměte podmínky použití, než se spouští, můžete definovat následující proměnné prostředí:<br/> -Název #1: `ACCEPT_EULA`<br/>– Hodnota #1: `Y`|
| **Výchozí CreateOptions field**  | CreateOptions field musí být kratší než 512 znaků. Musí být platný kód JSON, není uvozen řídicími znaky. Další informace o [CreateOptions field](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Například, pokud modul vyžaduje vytvoření vazby portu, můžete definovat následující CreateOptions field:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|

<br/> Vyberte **Uložit** SKU nastavení uložte. 

## <a name="next-steps"></a>Další postup

Použití [Marketplace kartu](./cpp-marketplace-tab.md) vytvořit popis nabídky marketplace.
