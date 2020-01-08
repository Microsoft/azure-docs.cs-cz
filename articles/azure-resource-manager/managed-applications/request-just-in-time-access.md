---
title: Vyžádání přístupu za běhu
description: Popisuje způsob, jakým vydavatelé Azure Managed Applications vyžadují přístup k spravované aplikaci za běhu.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651381"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Povolení a vyžádat přístup za běhu pro Azure Managed Applications

Příjemci spravované aplikace můžou být zdráhají, aby vám udělily trvalý přístup ke spravované skupině prostředků. Jako vydavatel aplikace manažera můžete chtít, aby spotřebitelé přesně znali, když potřebujete přístup ke spravovaným prostředkům. Aby spotřebitelé měli větší kontrolu nad udělením přístupu ke spravovaným prostředkům, Azure Managed Applications poskytují funkci s názvem přístup za běhu (JIT), která je aktuálně ve verzi Preview.

Přístup JIT vám umožní požádat o zvýšený přístup k prostředkům spravované aplikace pro účely řešení potíží nebo údržby. Vždy máte přístup k prostředkům jen pro čtení, ale v určitém časovém období můžete mít větší přístup.

Pracovní postup pro udělení přístupu:

1. Do Marketplace přidáte spravovanou aplikaci a určíte, že je k dispozici přístup JIT.

1. Během nasazení umožňuje spotřebiteli přístup JIT k této instanci spravované aplikace.

1. Po nasazení může příjemce změnit nastavení přístupu JIT.

1. Pokud potřebujete řešit nebo aktualizovat spravované prostředky, odešlete žádost o přístup.

1. Příjemce vaši žádost schválí.

Tento článek se zaměřuje na akce, které vydavatelé akcí využívají k povolení přístupu JIT a odesílání požadavků. Další informace o schvalování požadavků na přístup JIT najdete [v tématu schválení přístupu za běhu v Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Přidat krok přístupu JIT do uživatelského rozhraní

Soubor CreateUiDefinition. JSON se shoduje se stejným způsobem jako soubor uživatelského rozhraní, který vytvoříte pro trvalý přístup, s výjimkou, že musíte zahrnout krok, který umožňuje uživatelům povolit přístup JIT. Další informace o publikování první nabídky spravovaných aplikací v Azure Marketplace najdete v tématu [Azure Managed Applications na webu Marketplace](publish-marketplace-app.md).

Pro podporu schopnosti JIT pro vaši nabídku přidejte do souboru CreateUiDefinition. JSON následující obsah:

V "krocích":

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
 
V části "výstupy":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> Přístup JIT je ve verzi Preview. Schéma pro konfiguraci JIT se může v budoucích iteracích změnit.

## <a name="enable-jit-access"></a>Povolit přístup JIT

Při definování vaší nabídky na webu Marketplace se ujistěte, že jste povolili přístup JIT.

1. Přihlaste se k [portálu cloud Publishing partner](https://cloudpartner.azure.com).

1. Zadejte hodnoty pro publikování spravované aplikace na webu Marketplace. Vyberte **Ano** , pokud chcete **Povolit přístup JIT?**

   ![Povolit přístup za běhu](./media/request-just-in-time-access/marketplace-enable.png)

Přidali jste do svého uživatelského rozhraní krok konfigurace JIT a v nabídce Marketplace jste povolili přístup JIT. Když si uživatelé nasadí spravovanou aplikaci, můžou [zapnout přístup JIT ke své instanci](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Vyžádání přístup

Pokud potřebujete přístup ke spravovaným prostředkům uživatele, pošlete žádost o konkrétní roli, dobu a dobu trvání. Příjemce pak musí žádost schválit.

Odeslání požadavku na přístup JIT:

1. Pro spravovanou aplikaci vyberte **přístup JIT** , ke kterému potřebujete získat přístup.

1. Vyberte **oprávněné role**a u požadované role vyberte ve sloupci Akce možnost **aktivovat** .

   ![Aktivovat žádost o přístup](./media/request-just-in-time-access/send-request.png)

1. Na formuláři **role Aktivace** vyberte čas zahájení a dobu, po kterou má být vaše role aktivní. Kliknutím na tlačítko **aktivovat** odešlete žádost.

   ![Aktivovat přístup](./media/request-just-in-time-access/activate-access.png) 

1. Podívejte se na oznámení, abyste viděli, že se nová žádost o JIT úspěšně poslala příjemci.

   ![Oznámení](./media/request-just-in-time-access/in-progress.png)

   Nyní musíte počkat, až uživatel [žádost schválí](approve-just-in-time-access.md#approve-requests).

1. Chcete-li zobrazit stav všech požadavků JIT pro spravovanou aplikaci, vyberte možnost **přístup k JIT** a **historii žádostí**.

   ![Zobrazit stav](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Známé problémy

ID objektu zabezpečení účtu požadujícího přístup JIT musí být explicitně zahrnuto v definici spravované aplikace. Účet nejde zahrnout jenom ze skupiny, která je určená v balíčku. Toto omezení bude opraveno v budoucí verzi.

## <a name="next-steps"></a>Další kroky

Další informace o schvalování požadavků pro přístup JIT najdete v tématu [schválení přístupu za běhu v Azure Managed Applications](approve-just-in-time-access.md).
