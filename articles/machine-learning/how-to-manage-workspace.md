---
title: Vytváření pracovních prostorů na portálu
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet, zobrazovat a odstraňovat Azure Machine Learning pracovní prostory v Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1f68753e7b28163f3ec0c18c30cf5939e0bc5243
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89649412"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Vytváření a Správa pracovních prostorů Azure Machine Learning v Azure Portal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku vytvoříte, zobrazíte a odstraníte [**Azure Machine Learning pracovní prostory**](concept-workspace.md) v Azure Portal pro [Azure Machine Learning](overview-what-is-azure-ml.md).  Portál představuje nejjednodušší způsob, jak začít pracovat s pracovními prostory, ale jak se vaše potřeby mění nebo jsou požadavky na automatizaci, můžete také vytvořit a odstranit pracovní prostory [pomocí rozhraní](reference-azure-machine-learning-cli.md)příkazového řádku [s kódem Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) nebo [prostřednictvím rozšíření vs Code](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

K vytvoření pracovního prostoru potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů předplatného Azure. 

1. V levém horním rohu Azure Portal vyberte **+ vytvořit prostředek**.

      ![Vytvoření nového prostředku](./media/how-to-manage-workspace/create-workspace.gif)

1. K vyhledání **Machine Learning**použijte panel hledání.

1. Vyberte **Machine Learning**.

1. V podokně **Machine Learning** vyberte **vytvořit** a začněte.

1. Zadáním následujících informací nakonfigurujte nový pracovní prostor:

   Pole|Popis 
   ---|---
   Název pracovního prostoru |Zadejte jedinečný název, který identifikuje váš pracovní prostor. V tomto příkladu používáme **docs-WS**. Názvy musí být v rámci skupiny prostředků jedinečné. Použijte název, který se dá snadno vyvolat a odlišit z pracovních prostorů vytvořených jinými uživateli. V názvu pracovního prostoru se nerozlišují malá a velká písmena.
   Předplatné |Vyberte předplatné Azure, které chcete použít.
   Skupina prostředků | Použijte stávající skupinu prostředků, kterou máte v předplatném, nebo zadejte název a vytvořte novou skupinu prostředků. Skupina prostředků obsahuje související prostředky pro řešení Azure. V tomto příkladu používáme **docs-AML**. Chcete-li použít existující skupinu prostředků, potřebujete roli *Přispěvatel* nebo *vlastník* .  Další informace o přístupu najdete v tématu [Správa přístupu k pracovnímu prostoru Azure Machine Learning](how-to-assign-roles.md).
   Umístění | Vyberte umístění, které je nejblíže vašim uživatelům a datovým prostředkům, abyste mohli vytvořit pracovní prostor.
   Umístění | Vyberte umístění, které je nejblíže vašim uživatelům a datovým prostředkům, abyste mohli vytvořit pracovní prostor.
   Edice pracovního prostoru | Vyberte **Basic** nebo **Enterprise**.  Tato edice pracovního prostoru určuje funkce, ke kterým budete mít přístup a ceny. Přečtěte si další informace o [nabídkách Basic a Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Konfigurace pracovního prostoru](./media/how-to-manage-workspace/select-edition.png)

1. Po dokončení konfigurace pracovního prostoru vyberte **zkontrolovat + vytvořit**. Volitelně můžete pomocí oddílů [sítě](#networking) a [Upřesnit](#advanced) nakonfigurovat další nastavení pracovního prostoru.
2. Zkontrolujte nastavení a proveďte další změny nebo opravy. Až budete s nastavením spokojeni, vyberte **vytvořit**.

   > [!Warning] 
   > Vytvoření pracovního prostoru v cloudu může trvat několik minut.

   Po dokončení procesu se zobrazí zpráva o úspěšném nasazení. 
 
 1. Pokud chcete zobrazit nový pracovní prostor, vyberte **Přejít k prostředku**.


### <a name="networking"></a>Sítě

> [!IMPORTANT]
> Další informace o používání privátního koncového bodu a virtuální sítě s vaším pracovním prostorem najdete v tématu věnovaném [izolaci sítě a ochraně osobních údajů](how-to-enable-virtual-network.md).

1. Výchozí konfigurací sítě je použití __veřejného koncového bodu__, který je přístupný na veřejném Internetu. Pokud chcete omezit přístup k vašemu pracovnímu prostoru na Virtual Network Azure, kterou jste vytvořili, můžete místo toho vybrat jako __metodu připojení__ __privátní koncový bod__ (Preview) a pak pomocí __+ Přidat__ nakonfigurovat koncový bod.

   > [!IMPORTANT]
   > Používání privátního koncového bodu s Azure Machine Learning pracovním prostorem je v současnosti ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Výběr privátního koncového bodu":::

1. Ve formuláři __Vytvoření privátního koncového bodu__ nastavte umístění, název a virtuální síť, které se mají použít. Pokud chcete použít koncový bod se zónou Privátní DNS, vyberte možnost __integrovat s privátní zónou DNS__ a vyberte zónu pomocí pole __privátní DNS zóna__ . Vyberte __OK__ a vytvořte koncový bod. 

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Vytvoření privátního koncového bodu":::

1. Po dokončení konfigurace sítě můžete vybrat možnost __zkontrolovat + vytvořit__nebo přejít k volitelné __Rozšířené__ konfiguraci.

    > [!WARNING]
    > Při vytváření privátního koncového bodu se vytvoří nová zóna Privátní DNS s názvem __privatelink.API.AzureML.MS__ . Obsahuje odkaz na virtuální síť. Pokud vytvoříte více pracovních prostorů s privátními koncovými body ve stejné skupině prostředků, může být do zóny DNS přidána pouze virtuální síť pro první privátní koncový bod. Chcete-li přidat položky pro virtuální sítě používané dalšími koncovými body nebo soukromými koncovými body, použijte následující postup:
    > 
    > 1. V [Azure Portal](https://portal.azure.com)vyberte skupinu prostředků, která obsahuje pracovní prostor. Pak vyberte prostředek zóny Privátní DNS s názvem __privatelink.API.AzureML.MS__.
    > 2. V __Nastavení__vyberte __odkazy virtuální sítě__.
    > 3. Vyberte __Přidat__. Na stránce __Přidat virtuální síť__ zadejte jedinečný __název odkazu__a potom vyberte __virtuální síť__ , kterou chcete přidat. Kliknutím na __tlačítko OK__ přidejte síťové propojení.
    >
    > Další informace najdete v tématu [Konfigurace DNS privátního koncového bodu Azure](/azure/private-link/private-endpoint-dns).

### <a name="advanced"></a>Upřesnit

Ve výchozím nastavení se metriky a metadata pro pracovní prostor ukládají do Azure Cosmos DB instance, kterou Microsoft udržuje. Tato data se šifrují pomocí klíčů spravovaných Microsoftem. 

Pokud chcete omezit data, která Microsoft shromažďuje v pracovním prostoru, vyberte __vysoký pracovní prostor dopad na firmu__.

> [!IMPORTANT]
> Výběr vysokého dopadu na firmu se dá udělat jenom při vytváření pracovního prostoru. Po vytvoření pracovního prostoru toto nastavení nemůžete změnit.

Pokud používáte verzi Azure Machine Learning __Enterprise__ , můžete místo toho zadat vlastní klíč. Tím se vytvoří instance Azure Cosmos DB, která ukládá metriky a metadata v předplatném Azure. Použijte následující postup, chcete-li použít vlastní klíč:

> [!IMPORTANT]
> Před provedením tohoto postupu je třeba nejprve provést následující akce:
>
> 1. Autorizaci __aplikace Machine Learning__ (v části Správa identit a přístupu) s oprávněními přispěvatele v předplatném.
> 1. Postupujte podle kroků v části [konfigurace klíčů spravovaných zákazníkem](/azure/cosmos-db/how-to-setup-cmk) na:
>     * Registrace poskytovatele Azure Cosmos DB
>     * Vytvoření a konfigurace Azure Key Vault
>     * Vygenerovat klíč
>
>     Nemusíte vytvářet instanci Azure Cosmos DB ručně, ta se vytvoří během vytváření pracovního prostoru. Tato instance Azure Cosmos DB se vytvoří v samostatné skupině prostředků s použitím názvu založeného na tomto vzoru: `<your-resource-group-name>_<GUID>` .
>
> Po vytvoření pracovního prostoru toto nastavení nemůžete změnit. Pokud odstraníte Azure Cosmos DB, kterou používá váš pracovní prostor, musíte také odstranit pracovní prostor, který ho používá.

1. Vyberte __klíče spravované zákazníkem__a pak vyberte __možnost kliknutím vyberte klíč__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Klíče spravované zákazníkem":::

1. Na Azure Key Vault ve formuláři __Vyberte klíč__ vyberte existující Azure Key Vault, klíč, který obsahuje, a verzi klíče. Tento klíč slouží k šifrování dat uložených v Azure Cosmos DB. Nakonec k použití tohoto klíče použijte tlačítko __Vybrat__ .

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Vyberte klíč.":::

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Upgrade na Enterprise Edition

Pracovní prostor můžete upgradovat ze sady Basic Edition na Enterprise Edition a využít tak vylepšené funkce, jako je například prostředí s nízkým kódem a rozšířené funkce zabezpečení.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. Vyberte pracovní prostor, který chcete upgradovat.

1. V pravém horním rohu stránky vyberte **Další informace**  .

   [![Upgrade pracovního prostoru ](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. V zobrazeném okně vyberte **upgrade** .


> [!IMPORTANT]
> Pracovní prostor Enterprise Edition nelze downgradovat na pracovní prostor edice Basic.

### <a name="download-a-configuration-file"></a>Stažení konfiguračního souboru

1. Pokud budete vytvářet [výpočetní instanci](tutorial-1st-experiment-sdk-setup.md#azure), tento krok přeskočte.

1. Pokud máte v úmyslu používat v místním prostředí kód, který odkazuje na tento pracovní prostor, vyberte  **stáhnout config.jsv** části **Přehled** v pracovním prostoru.  

   ![Stáhnout config.js](./media/how-to-manage-workspace/configure.png)
   
   Soubor umístěte do struktury adresáře pomocí skriptů Pythonu nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři. Při vytváření výpočetní instance se tento soubor přidá do správného adresáře na virtuálním počítači za vás.

## <a name="find-a-workspace"></a><a name="view"></a>Najít pracovní prostor

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Do pole nejvyšší hledání zadejte **Machine Learning**.  

1. Vyberte **Machine Learning**.

   ![Vyhledat Azure Machine Learning pracovní prostor](./media/how-to-manage-workspace/find-workspaces.png)

1. Prohlédněte si seznam pracovních prostorů, které se našly. Můžete filtrovat podle předplatného, skupin prostředků a umístění.  

1. Vyberte pracovní prostor, ve kterém chcete zobrazit jeho vlastnosti.

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

V [Azure Portal](https://portal.azure.com/)v horní části pracovního prostoru, který chcete odstranit, vyberte **Odstranit**  .

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Odstranit pracovní prostor":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby poskytovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesun pracovního prostoru

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

### <a name="deleting-the-azure-container-registry"></a>Odstranění Azure Container Registry

Azure Machine Learning pracovní prostor používá pro některé operace Azure Container Registry (ACR). Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Další kroky

Postupujte podle kurzu pro celou délku a Naučte se používat pracovní prostor k sestavování, vytváření výukových a nasazování modelů pomocí Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: modely vlaků](tutorial-train-models-with-aml.md)
