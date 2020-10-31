---
title: Firewall webových aplikací Azure a Azure Policy
description: Firewall webových aplikací Azure (WAF) v kombinaci s Azure Policy může pomáhat vyhovět podnikovým standardům a vyhodnocovat dodržování předpisů pro prostředky WAF v rozsahu.
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 6f05bd5a9798f2feec4424474a63625f812e7148
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100588"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Firewall webových aplikací Azure a Azure Policy

Firewall webových aplikací Azure (WAF) v kombinaci s Azure Policy může pomáhat vyhovět standardům organizace a vyhodnocovat dodržování předpisů pro prostředky WAF v rozsahu. Azure Policy je nástroj zásad správného řízení, který poskytuje agregované zobrazení pro vyhodnocení celkového stavu prostředí s možností přechodu k podrobnostem na jednotlivé prostředky a členitosti podle zásad. Zásady Azure také pomáhají přenést prostředky do dodržování předpisů prostřednictvím hromadné nápravy pro stávající prostředky a automatickou nápravu nových prostředků.

## <a name="azure-policy-for-web-application-firewall"></a>Azure Policy pro Firewall webových aplikací

K dispozici je několik předdefinovaných Azure Policy definic pro správu prostředků WAF. Rozpis definic zásad a jejich funkce jsou následující:

1. **Firewall webových aplikací (WAF) by měl být povolený pro službu Azure front-dveří** : služby Azure front-dveří jsou vyhodnoceny, pokud existuje WAF při vytváření prostředků. Zásady mají tři důsledky: audit, DENY a Disable. Audit sleduje, když služba Azure front-dveří nemá WAF a umožňuje uživatelům zjistit, co služba Azure front dveří nedodržuje. Při odepření se zabrání vytvoření služby Azure front-dveří, pokud není WAF připojené. Disabled vypne tuto zásadu.

2. **Firewall webových aplikací (WAF) by měla být povolená pro Application Gateway** : aplikační brány se vyhodnocují, pokud existuje WAF při vytváření prostředků. Zásady mají tři důsledky: audit, DENY a Disable. Audit sleduje, když Application Gateway nemá WAF a umožňuje uživatelům zjistit, co Application Gateway nedodržuje. Deny zabrání v vytváření jakýchkoli Application Gateway, pokud není připojen WAF. Disabled vypne tuto zásadu.

3. **Firewall webových aplikací (WAF) by měl používat zadaný režim pro službu Azure front-dveří** : vyžaduje, aby bylo použití režimu detekce nebo prevence aktivní ve všech zásadách firewallu webových aplikací pro službu Azure front-dveří. Zásady mají tři důsledky: audit, DENY a Disable. Audit sleduje, když se WAF nevejde do zadaného režimu. Deny zabrání v vytváření jakýchkoli WAF, pokud není ve správném režimu. Disabled vypne tuto zásadu.

4. **Firewall webových aplikací (WAF) by měl používat zadaný režim pro Application Gateway** : v zásadách firewallu webových aplikací pro Application Gateway musí být použití režimu detekce nebo prevence aktivní. Zásady mají tři důsledky: audit, DENY a Disable. Audit sleduje, když se WAF nevejde do zadaného režimu. Deny zabrání v vytváření jakýchkoli WAF, pokud není ve správném režimu. Disabled vypne tuto zásadu.


## <a name="launch-an-azure-policy"></a>Spustit Azure Policy


1.  Na domovské stránce Azure zadejte do panelu hledání zásady a klikněte na ikonu Azure Policy

2.  Ve službě Azure Policy v části **vytváření obsahu** vyberte **přiřazení** .

[!div class="mx-imgBorder"]
![Firewall webových aplikací Azure](../media/waf-azure-policy/policy-home.png)

3.  Na stránce přiřazení vyberte ikonu **přiřadit zásadu** v horní části.

[!div class="mx-imgBorder"]
![Snímek obrazovky zobrazující kartu základy na stránce přiřadit zásady](../media/waf-azure-policy/assign-policy.png)

4.  Na kartě základy stránky pro přiřazení zásad aktualizujte následující pole:
    1.  **Rozsah** : vyberte, na jaké Předplatné a skupiny prostředků Azure by měla být ovlivněna definice zásad.
    2.  **Vyloučení** : vyberte všechny prostředky z oboru, které chcete vyloučit z přiřazení zásad.
    3.  **Definice zásady** : Vyberte definici zásady, která se má použít pro obor s vyloučeními. Na panelu hledání zadejte "Firewall webových aplikací" a vyberte odpovídající Azure Policy firewallu webových aplikací.

[!div class="mx-imgBorder"]
![Snímek obrazovky zobrazující dostupné definice zásad](../media/waf-azure-policy/policy-listing.png)


5.  Vyberte kartu **parametry** a aktualizujte parametry přiřazení zásad. Pro lepší objasnění toho, co parametr dělá, najeďte myší na ikonu informace vedle názvu parametru pro další objasnění.

6.  Kliknutím na tlačítko **zkontrolovat + vytvořit** dokončete přiřazení zásad. Přiřazení zásady trvá přibližně 15 minut, dokud nebude aktivní pro nové prostředky.
