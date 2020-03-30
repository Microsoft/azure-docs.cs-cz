---
title: Vyžádání přístupu za běhu
description: Popisuje, jak vydavatelé spravovaných aplikací Azure požadují přístup ke spravované aplikaci za čas.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651381"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Povolení a vyžádání přístupu just-in-time pro spravované aplikace Azure

Spotřebitelé spravované aplikace se mohou zdráhat udělit trvalý přístup ke spravované skupině prostředků. Jako vydavatel aplikace správce můžete dát přednost tomu, aby spotřebitelé přesně věděli, kdy potřebujete přístup ke spravovaným prostředkům. Aby měli spotřebitelé větší kontrolu nad udělováním přístupu ke spravovaným prostředkům, azure spravované aplikace poskytuje funkci nazvanou přístup za čas (JIT), která je aktuálně ve verzi preview.

Přístup JIT umožňuje požádat o zvýšený přístup k prostředkům spravované aplikace pro řešení potíží nebo údržbu. Vždy máte přístup jen pro čtení k prostředkům, ale pro určité časové období můžete mít větší přístup.

Pracovní tok pro udělení přístupu je:

1. Přidáte spravovanou aplikaci na marketplace a určit, že přístup JIT je k dispozici.

1. Během nasazení příjemce povolí přístup JIT pro tuto instanci spravované aplikace.

1. Po nasazení může spotřebitel změnit nastavení přístupu JIT.

1. Žádost o přístup odešlete v případě, že potřebujete vyřešit potíže se spravovanými prostředky nebo je aktualizovat.

1. Spotřebitel vaši žádost schválí.

Tento článek se zaměřuje na akce vydavatelé provést povolit přístup JIT a odesílat požadavky. Informace o schvalování žádostí o přístup JIT najdete [v tématu Schválení přístupu za čas ve spravovaných aplikacích Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Přidat krok přístupu JIT do ui

Soubor CreateUiDefinition.json je přesně jako soubor ui, který vytvoříte pro trvalý přístup, s tím rozdílem, že je nutné zahrnout krok, který umožňuje spotřebitelům povolit přístup JIT. Další informace o publikování první nabídky spravovaných aplikací na Azure Marketplace najdete v [tématu Spravované aplikace Azure na Marketplace](publish-marketplace-app.md).

Chcete-li podporovat funkci JIT pro vaši nabídku, přidejte do souboru CreateUiDefinition.json následující obsah:

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
 
V "výstupech":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> Jit přístup je ve verzi preview. Schéma pro konfiguraci JIT může změnit v budoucích iterací.

## <a name="enable-jit-access"></a>Povolení přístupu JIT

Při definování nabídky na trhu, ujistěte se, že povolíte přístup JIT.

1. Přihlaste se k [portálu pro publikování cloudových partnerů](https://cloudpartner.azure.com).

1. Zadejte hodnoty pro publikování spravované aplikace na webu Marketplace. Chcete-li **povolit přístup JIT, vyberte** **možnost Ano?**

   ![Povolení přístupu za čase](./media/request-just-in-time-access/marketplace-enable.png)

Přidali jste krok konfigurace JIT do vašeho ui a povolili přístup JIT v nabídce marketplace. Když spotřebitelé nasazují spravovanou aplikaci, můžou [pro svou instanci zapnout přístup JIT](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Vyžádání přístup

Pokud potřebujete získat přístup ke spravovaným prostředkům spotřebitele, odešlete žádost o konkrétní roli, čas a dobu trvání. Spotřebitel pak musí žádost schválit.

Odeslání žádosti o přístup jit:

1. Vyberte **JIT Access** pro spravovanou aplikaci, ke které potřebujete přístup.

1. Vyberte **Způsobilé role**a ve sloupci AKCE pro požadovanou roli vyberte **Aktivovat.**

   ![Aktivovat žádost o přístup](./media/request-just-in-time-access/send-request.png)

1. Ve formuláři **Aktivovat roli** vyberte čas zahájení a dobu trvání, aby byla vaše role aktivní. **Chcete-li odeslat** požadavek, vyberte možnost Aktivovat.

   ![Aktivace přístupu](./media/request-just-in-time-access/activate-access.png) 

1. Zobrazení oznámení zobrazíte, že nový požadavek JIT je úspěšně odeslána spotřebiteli.

   ![Oznámení](./media/request-just-in-time-access/in-progress.png)

   Nyní musíte počkat, až spotřebitel [vaši žádost schválí](approve-just-in-time-access.md#approve-requests).

1. Chcete-li zobrazit stav všech požadavků JIT pro spravovanou aplikaci, vyberte **možnost Historie přístupu** a požadavků **JIT**.

   ![Zobrazit stav](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Známé problémy

ID jistiny účtu požadující přístup k JIT musí být explicitně zahrnuty v definici spravované aplikace. Účet nelze zahrnout pouze prostřednictvím skupiny, která je určena v balíčku. Toto omezení bude opraveno v budoucí verzi.

## <a name="next-steps"></a>Další kroky

Informace o schvalování žádostí o přístup JIT najdete [v tématu Schválení přístupu just-in-time ve spravovaných aplikacích Azure](approve-just-in-time-access.md).
