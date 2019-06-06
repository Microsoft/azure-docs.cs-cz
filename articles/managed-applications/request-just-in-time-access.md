---
title: Povolit a žádat o přístup just-in-time pro spravované aplikace Azure
description: Popisuje, jak vydavatelé spravovaných aplikací Azure o just-in-time přístup ke spravované aplikaci.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481771"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Povolit a žádat o přístup just-in-time pro spravované aplikace Azure

Příjemci spravované aplikace může být zdráhají udělit trvalý přístup ke spravované skupině prostředků. Jako vydavatel aplikace pro správu můžete chtít, aby spotřebitelé věděli, přesně, když potřebujete přístup ke spravované prostředky. Poskytnout příjemci větší kontrolu nad udělení přístupu ke spravovaným prostředkům Azure Managed Applications poskytuje funkci s názvem přístup just-in-time (JIT), která je aktuálně ve verzi preview.

Přístup JIT umožňuje požádat o přístup se zvýšeným oprávněním k prostředkům spravované aplikace pro řešení potíží nebo údržbě. Vždy máte přístup jen pro čtení k prostředkům, ale pro určité časové období můžete mít vyšší úroveň přístupu.

Pracovní postup pro udělení přístupu je:

1. Přidat spravované aplikace na webu Marketplace a určit, že je k dispozici přístup JIT.

1. Během nasazování umožňuje příjemci přístup JIT pro tuto instanci spravované aplikace.

1. Po nasazení uživatel může změnit nastavení pro přístup JIT.

1. Pokud potřebujete vyřešit nebo aktualizovat spravované prostředky, odešlete žádost o přístup.

1. Příjemce potvrdí vaši žádost.

Tento článek se zaměřuje na akce, které vydavatelé chtít povolit přístup JIT a požadavky můžete odesílat. Další informace o schvalování žádosti o přístup JIT, naleznete v tématu [schvalovat přístup za běhu v Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Přidejte krok přístup JIT k uživatelskému rozhraní

Váš soubor CreateUiDefinition.json je úplně stejně jako soubor uživatelského rozhraní, které vytvoříte pro permanentní přístup, ale je nutné zadat krok, který umožňuje povolit přístup JIT příjemci. Další informace o publikování vaší nabídky na webu Azure Marketplace první spravované aplikace, najdete v článku [spravované aplikace Azure na webu Marketplace](publish-marketplace-app.md).

K podpoře funkce JIT pro vaši nabídku, přidejte do souboru CreateUiDefinition.json následujícím obsahem:

V "takto":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
V "výstupy":

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> Přístup JIT je ve verzi preview. Schéma konfigurace JIT může změnit v budoucích iterací.

## <a name="enable-jit-access"></a>Povolit přístup JIT

Při definování vaší nabídky na webu Marketplace, ujistěte se, že jste povolili přístup JIT.

1. Přihlaste se k [publikování portál partnerů cloudu](https://cloudpartner.azure.com).

1. Zadejte hodnoty, které publikování spravované aplikace na webu Marketplace. Vyberte **Ano** pro **povolit přístup JIT?**

   ![Povolení přístupu just-in-time](./media/request-just-in-time-access/marketplace-enable.png)

Přidání kroku konfigurace JIT do uživatelské rozhraní a jste povolili přístup JIT v nabídce marketplace. Když příjemci nasazení spravované aplikace, mohou [zapnout přístup JIT na jejich instanci](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Vyžádat si přístup

Když budete potřebovat pro přístup k prostředkům spravované paměti spotřebitele, pošlete žádost pro konkrétní role, čas a dobu trvání. Příjemce potom musíte tuto žádost schválit.

Odešlete žádost o přístup JIT:

1. Vyberte **přístup JIT** pro spravovanou aplikaci, potřebujete získat přístup.

1. Vyberte **oprávněných rolí**a vyberte **aktivovat** ve sloupci Akce pro danou roli chcete.

   ![Aktivovat žádost o přístup](./media/request-just-in-time-access/send-request.png)

1. Na **aktivovat roli** formuláře, vyberte počáteční čas a dobu trvání pro vaší role jako aktivní. Vyberte **aktivovat** odešlete žádost.

   ![Aktivovat přístup do](./media/request-just-in-time-access/activate-access.png) 

1. Zobrazte oznámení, pokud chcete zobrazit, že nový požadavek JIT je úspěšně odeslána příjemci.

   ![Oznámení](./media/request-just-in-time-access/in-progress.png)

   Nyní, je nutné počkat spotřebitele [potvrzovat svou žádost](approve-just-in-time-access.md#approve-requests).

1. Chcete-li zobrazit stav všech žádostí o JIT pro spravovanou aplikaci, vyberte **přístup JIT** a **historie žádostí**.

   ![Zobrazit stav](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Známé problémy

ID objektu zabezpečení účtu si vyžádat přístup JIT musí být explicitně součástí definice spravované aplikace. Tento účet pouze nelze zahrnout do skupiny, které je zadané v balíčku. Toto omezení bude opraven v budoucí verzi.

## <a name="next-steps"></a>Další postup

Další informace o schvalování žádosti o přístup JIT, naleznete v tématu [schvalovat přístup za běhu v Azure Managed Applications](approve-just-in-time-access.md).
