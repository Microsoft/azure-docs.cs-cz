---
title: Revize v Azure API Management | Microsoft Docs
description: Přečtěte si o konceptu revizí v Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: bd837faaaa986659ad9b30aa3cf853ea490cec6d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812133"
---
# <a name="revisions-in-azure-api-management"></a>Revize v Azure API Management

Revize umožňují provádět změny vašich rozhraní API řízeným a bezpečným způsobem. Pokud chcete provést změny, vytvořte novou revizi. ROZHRANÍ API pak můžete upravovat a testovat bez narušení vašich uživatelů rozhraní API. Až budete připraveni, provedete svou revizi jako aktuální. V současné době můžete volitelně odeslat položku do protokolu změn a zajistit tak aktuálnost vašich uživatelů rozhraní API s tím, co se změnilo. Protokol změn se publikuje na portálu pro vývojáře.

> [!NOTE]
> Portál pro vývojáře není dostupný v úrovni spotřeby.

S revizemi můžete:

- Bezpečně Provádějte změny v definicích a zásadách rozhraní API, aniž byste narušili provozní rozhraní API.
- Před publikováním si zkuste změny vyzkoušet.
- Poznamenejte si změny, které provedete, aby vaši vývojáři mohli pochopit, co je nového.
- Pokud zjistíte problémy, vraťte se zpátky.

[Začněte s revizemi podle našeho návodu.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Přístup k určitým revizím

K každé revizi rozhraní API lze použít speciálně vytvořenou adresu URL. Připojením `;rev={revisionNumber}` na konec adresy URL rozhraní API, ale před řetězcem dotazu získáte přístup ke konkrétní revizi rozhraní API. Tuto adresu URL můžete například použít pro přístup k revizi 3 `customers` rozhraní API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Ve výchozím nastavení má každá revize stejné nastavení zabezpečení jako aktuální revize. Zásady pro konkrétní revizi můžete záměrně změnit, pokud chcete mít pro každou revizi použitu různé zabezpečení. Například můžete chtít přidat [zásadu filtrování IP adres](./api-management-access-restriction-policies.md#RestrictCallerIPs) , která externím volajícím brání v přístupu k revizi, která je stále ve vývoji.

Revizi lze převést do režimu offline, která je nepřístupná volajícím, i když se pokusí získat přístup k revizi prostřednictvím její adresy URL. Revizi můžete označit jako offline pomocí Azure Portal. Pokud používáte PowerShell, můžete použít `Set-AzApiManagementApiRevision` rutinu a nastavit `Path` argument na `$null` .

> [!NOTE]
> Doporučujeme, abyste převedli revize v režimu offline, když je nepoužíváte k testování.

## <a name="current-revision"></a>Aktuální revize

Jednu revizi lze nastavit jako *aktuální* revizi. Tato revize bude použita pro všechny požadavky rozhraní API, které neurčují explicitní číslo revize v adrese URL. Vrátit se k předchozí revizi můžete tak, že nastavíte tuto revizi jako aktuální.

Revizi můžete nastavit jako aktuální pomocí Azure Portal. Pokud používáte PowerShell, můžete použít `New-AzApiManagementApiRelease` rutinu.

## <a name="revision-descriptions"></a>Popisy revizí

Když vytvoříte revizi, můžete nastavit popis pro vlastní účely sledování. Popisy nejsou přehrávány vašim uživatelům rozhraní API.

Když nastavíte revizi jako aktuální, můžete také volitelně zadat poznámku o veřejném protokolu změn. Protokol změn je součástí portálu pro vývojáře, aby si uživatelé rozhraní API mohli zobrazit. Poznámky k protokolu změn můžete upravit pomocí `Update-AzApiManagementApiRelease` rutiny prostředí PowerShell.

## <a name="versions-and-revisions"></a>Verze a revize

Verze a revize jsou odlišné funkce. Každá verze může mít více revizí, podobně jako rozhraní API bez verzí. Můžete použít revize bez použití verze nebo jiného způsobu kolem. Obvykle se verze používají k oddělení verzí rozhraní API s zásadními změnami, zatímco revize lze použít pro méně závažné a nepodstatné změny v rozhraní API.

Pokud zjistíte, že vaše revize má zásadní změny, nebo pokud chcete, aby se vaše revize nastavila na verzi beta/testu, můžete z revize vytvořit verzi. Pomocí Azure Portal klikněte na možnost vytvořit verzi z revize v místní nabídce revize na kartě Revize.
