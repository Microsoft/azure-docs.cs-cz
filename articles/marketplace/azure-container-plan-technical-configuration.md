---
title: V Microsoft AppSource nastavte plán Technical Configuration pro nabídku kontejneru Azure.
description: V Microsoft AppSource nastavte plán Technical Configuration pro nabídku kontejneru Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: dfee13f31509fedc9558befeaaeebadedf2104de
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892611"
---
# <a name="set-plan-technical-configuration-for-an-azure-container-offer"></a>Nastavení technické konfigurace plánu pro nabídku Azure Container

Image kontejneru musí být hostované v privátním [Azure Container Registry](https://azure.microsoft.com/services/container-registry/). Tato stránka slouží k poskytnutí referenčních informací pro úložiště imagí kontejneru v rámci Azure Container Registry.

Po odeslání nabídky se vaše image kontejneru zkopíruje do Azure Marketplace v konkrétním veřejném registru kontejneru. Všechny požadavky uživatelů Azure na použití vašeho modulu se obsluhují z Azure Marketplaceho veřejného kontejneru kontejnerů, nikoli z vašeho privátního registru kontejneru.

Můžete cílit na více platforem a zadat několik verzí imagí kontejneru modulu pomocí značek. Další informace o značkách a verzích najdete v tématu [Příprava prostředků Azure Container Technical assets](azure-container-technical-assets.md).

## <a name="image-repository-details"></a>Podrobnosti o úložišti imagí

Zadejte **ID předplatného Azure** , kde se oznamuje využití prostředků, a účtují se služby Azure Container Registry, které obsahují vaši image kontejneru. Toto ID najdete na [stránce Předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) v Azure Portal.

Zadejte [**název skupiny prostředků Azure**](../azure-resource-manager/management/manage-resource-groups-portal.md) , který obsahuje Azure Container Registry s vaší imagí kontejneru. Skupina prostředků musí být přístupná v ID předplatného (výše). Název můžete najít na stránce [skupiny prostředků](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) v Azure Portal.

Zadejte [**název služby Azure Container Registry**](../container-registry/container-registry-intro.md) , který obsahuje vaši image kontejneru. V rámci skupiny prostředků Azure, kterou jste zadali dříve, se musí vyskytovat registr kontejnerů. Zadejte pouze název registru, nikoli úplný název přihlašovacího serveru. Vynechejte **azurecr.IO** z názvu. Název registru najdete na [stránce Registry kontejneru](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) v Azure Portal.

Zadejte [**uživatelské jméno správce pro Azure Container Registry**](../container-registry/container-registry-authentication.md#admin-account) přidružené k Azure Container Registry s vaší imagí kontejneru. Uživatelské jméno a heslo (další krok) se vyžaduje, aby vaše společnost měla přístup k registru. Pokud chcete získat uživatelské jméno a heslo správce, nastavte vlastnost s **povoleným správcem** na **hodnotu true** pomocí rozhraní příkazového řádku (CLI) pro Azure Command-Line. Volitelně můžete nastavit **uživatele správce** na **Povolení** v Azure Portal.

:::image type="content" source="media/azure-container/azure-create-12-update-container-registry-edit.png" alt-text="Ukazuje dialogové okno aktualizace registru kontejneru.":::

Zadejte **heslo správce pro Azure Container Registry** pro uživatelské jméno správce, které je přidružené k Azure Container registry a má vaši image kontejneru. Aby vaše společnost měla přístup k registru, je nutné zadat uživatelské jméno a heslo. Heslo z Azure Portal můžete získat tak, že v **Container Registry**  >  **přístupové klávesy** nebo pomocí Azure CLI použijete [příkaz show](/cli/azure/acr/credential#az-acr-credential-show).

:::image type="content" source="media/azure-container/azure-create-13-access-keys.png" alt-text="Ukazuje obrazovku přístupového klíče v Azure Portal.":::

Zadejte **název úložiště v rámci Azure Container Registry** , který obsahuje váš obrázek. Při odesílání obrázku do registru zadáte název úložiště. Název úložiště můžete najít tak, že kliknete na stránku [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **úložišť**. Další informace najdete v tématu [zobrazení úložišť registru kontejnerů v Azure Portal](../container-registry/container-registry-repositories.md). Po nastavení je název nelze změnit. Pro každou nabídku ve vašem účtu použijte jedinečný název.

## <a name="image-versions"></a>Verze bitové kopie

Zákazníci musí být schopni při publikování aktualizace automaticky získávat aktualizace z Azure Marketplace. Pokud se nechtějí aktualizovat, musí být schopní zůstat na konkrétní verzi image. To můžete provést přidáním nových značek obrázků pokaždé, když provedete aktualizaci image.

Pokud chcete zahrnout **značku obrázku** , která odkazuje na nejnovější verzi image na všech podporovaných platformách, vyberte **Přidat verzi image** . Musí také obsahovat značku verze (například počínaje xx. xx. xx, kde XX je číslo). Zákazníci by měli používat [značky manifestu](https://github.com/estesp/manifest-tool) k cílení na více platforem. Všechny značky, na které se odkazuje značka manifestu, musí být také přidané, aby je bylo možné nahrát. Všechny značky manifestu (s výjimkou nejnovější značky) musí začínat znakem X. Y nebo X. Y. Z-, kde X, Y a Z jsou celá čísla. Například pokud poslední značka odkazuje na `1.0.1-linux-x64` , `1.0.1-linux-arm32` , a `1.0.1-windows-arm32` , je nutné do tohoto pole přidat tyto šest značek. Podrobnosti o značkách a verzích najdete v tématu [Příprava prostředků Azure Container Technical assets](azure-container-technical-assets.md).

> [!TIP]
> Přidejte do obrázku značku testu, abyste mohli identifikovat obrázek během testování.

<!-- possible future restore

## Samples

These examples show how the plan listing fields appear in different views.

These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/azure-container/azure-create-10-plan-details-mtplc.png" alt-text="Illustrates the fields you see when viewing plan details in Azure Marketplace.":::

These are plan details on the Azure portal:

:::image type="content" source="media/azure-container/azure-create-11-plan-details-portal.png" alt-text="Illustrates plan details on the Azure portal.":::

Select **Save draft**, then **← Plan overview**  in the left-nav menu to return to the plan overview page.
-->
## <a name="next-steps"></a>Další kroky

- Pokud chcete **společně prodávat s Microsoftem** (volitelné), vyberte ho v nabídce vlevo a navigovat. Podrobnosti najdete v tématu [zapojení partnerů spoluprodejně](marketplace-co-sell.md).
- Pokud chcete **proprodávat prostřednictvím CSP** (taky partneři Cloud Solution partner), vyberte ho v nabídce vlevo a navigovat. Podrobnosti najdete v tématu věnovaném [prodeji prostřednictvím partnerů CSP](cloud-solution-providers.md).
- Pokud nenastavujete žádné z těchto nebo jste dokončili, je čas [si tuto nabídku zkontrolovat a publikovat](review-publish-offer.md).
