---
title: Přehled vlastních poskytovatelů Azure ve verzi Preview
description: Popisuje koncepty pro vytvoření poskytovatele vlastních prostředků pomocí Azure Resource Manageru
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159852"
---
# <a name="azure-custom-providers-preview-overview"></a>Přehled služby Azure ve verzi Preview Vlastní zprostředkovatelé

S Azure Vlastní zprostředkovatelé můžete rozšířit Azure pro práci s vaší službou. Můžete vytvořit vlastní poskytovatele prostředků, včetně typů přizpůsobené prostředků a akcí. Vlastní zprostředkovatel je integrovaná s Azure Resource Manageru. Funkce služby Správce prostředků, jako je například nasazení šablon a řízení přístupu na základě rolí, můžete použít k nasazení a zabezpečení služby.

Tento článek obsahuje přehled vlastních poskytovatelů a její možnosti. Následující obrázek ukazuje pracovní postup pro prostředky a akce definované ve vlastního zprostředkovatele.

![Přehled vlastního zprostředkovatele](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Vlastní zprostředkovatelé je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Definování vlastního zprostředkovatele

Začnete tím, že Azure Resource Manageru vědět o vlastního poskytovatele. Nasazení do Azure vlastního poskytovatele prostředků, který používá typ prostředku **Microsoft.CustomProviders/resourceProviders**. V tomto prostředku definujete prostředkům a akcím pro vaši službu.

Například, pokud vaše služba potřebuje typ prostředku s názvem **uživatelé**, zahrnují tento typ prostředku v definici vlastního zprostředkovatele. Pro každý typ prostředku zadáte koncový bod, který nabízí operace REST (PUT, GET, DELETE) pro daný typ prostředku. Koncový bod je možné hostovat v jakémkoli prostředí, a obsahuje logiku pro způsob, jakým služby zpracovává operace na typu prostředku.

Můžete také definovat vlastní akce pro poskytovatele prostředků. Akce představují operací POST. Pomocí akcí pro operace, jako je spuštění, zastavení nebo restartování. Zadáte koncový bod, který zpracovává požadavek.

Následující příklad ukazuje, jak definovat vlastní zprostředkovatel s akci a typ prostředku.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

Pro **routingType**, přípustné hodnoty jsou `Proxy` a `Cache`. Proxy server znamená, že požadavky pro typ prostředku nebo akce jsou zpracovány koncový bod. Nastavení mezipaměti je podporována pouze pro typy prostředků, ne akce. Pokud chcete zadat mezipaměti, musíte zadat také proxy serveru. Mezipaměť znamená, že odpovědi z koncového bodu jsou uložena za účelem optimalizace operací čtení. Pomocí nastavení mezipaměti usnadňuje implementaci rozhraní API, které je konzistentní a kompatibilní s jinými službami Resource Manageru.

## <a name="deploy-your-resource-types"></a>Nasazení vaše typy prostředků

Po definování vlastního poskytovatele, můžete nasadit vlastní prostředek typů. Následující příklad ukazuje kód JSON, zda že jste zahrnuli do šablony nasazení typ prostředku pro vlastního poskytovatele. Tento typ prostředku je možné nasadit do stejné šablony v jiných prostředcích Azure.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Správa přístupu

Použití Azure [řízení přístupu na základě rolí](../role-based-access-control/overview.md) ke správě přístupu k poskytovateli prostředků. Můžete přiřadit [předdefinované role](../role-based-access-control/built-in-roles.md) například vlastník, Přispěvatel nebo Čtenář pro uživatele. Nebo můžete definovat [vlastní role](../role-based-access-control/custom-roles.md) , které jsou specifické pro operace ve zprostředkovateli prostředků.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o vlastní poskytovatele. Přejdete na další článek k vytvoření vlastního zprostředkovatele.

> [!div class="nextstepaction"]
> [Kurz: Vytvoření vlastního zprostředkovatele a nasazení vlastních prostředků](create-custom-provider.md)
