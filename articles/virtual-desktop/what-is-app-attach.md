---
title: MSIX připojení k aplikaci Virtual Desktop ve Windows – přehled – Azure
description: Co je připojení aplikace MSIX? Informace najdete v tomto článku.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88556239"
---
# <a name="what-is-msix-app-attach"></a>Co je připojení aplikace MSIX?

MSIX je nový formát balení, který nabízí mnoho funkcí určených ke zlepšení prostředí pro vytváření balíčků pro všechny aplikace pro Windows. Další informace o MSIX najdete v tématu [Přehled MSIX](/windows/msix/overview).

Připojení aplikace MSIX je způsob, jak doručovat aplikace MSIX fyzickému i virtuálnímu počítači. Připojení aplikace MSIX se ale liší od pravidelného MSIX, protože je vytvořené hlavně pro virtuální počítače s Windows. Tento článek popisuje, co je aplikace MSIX připojit a co s tím může dělat.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Možnosti doručování aplikací ve virtuálním počítači s Windows

Aplikace můžete ve virtuálním počítači s Windows doručovat pomocí jedné z následujících metod:

- Vložení aplikací do hlavní image
- Použití nástrojů jako SCCM nebo Intune pro centrální správu
- Zřizování dynamických aplikací (AppV, VMWare AppVolumes nebo Citrix AppLayering)
- Vytváření vlastních nástrojů nebo skriptů pomocí Microsoftu a nástroje třetí strany

## <a name="what-does-msix-app-attach-do"></a>K čemu se aplikace MSIX připojuje?

V nasazení virtuálních klientských počítačů s Windows může připojení aplikace MSIX:

- Vytvářejte oddělení dat o uživatelích, operačním systému a aplikacích pomocí [kontejnerů MSIX](/windows/msix/msix-container).
- Odstraňte nutnost opětovného sbalení při dynamickém doručování aplikací.
- Zkraťte dobu potřebnou k přihlášení uživatele.
- Snižte nároky na infrastrukturu a náklady.

Připojení aplikace MSIX se musí použít mimo VDI nebo mezipaměť softwarové sběrnice.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Tradiční vrstva aplikací v porovnání s připojením aplikace MSIX

Následující tabulka porovnává klíčové funkce připojení aplikace MSIX a vrstvení aplikací.

| Funkce | Tradiční vrstvení aplikací  | Připojení aplikace MSIX  |
|-----|-----------------------------|--------------------|
| Formát               | Různé technologie vrstev aplikací vyžadují různé speciální formáty. | Funguje s nativním formátem balíčku MSIX.        |
| Opětovné sbalení režie | Speciální formáty vyžadují sekvencování a opětovné sbalení na aktualizaci.         | Aplikace publikované jako MSIX nevyžadují opětovné sbalení. Pokud však není balíček MSIX k dispozici, stále platí další režie. |
| Ekosystém            | Není k dispozici (například dodavatelé nedodali App-V)  | MSIX je podniková technologie od Microsoftu, která pro klíčové partnery ISV a interní aplikace, jako je Office, přijímá. MSIX můžete použít na virtuálních plochách i fyzických počítačích s Windows. |
| Infrastruktura       | Vyžaduje se další infrastruktura (servery, klienti a tak dále). | Jenom úložiště   |
| Správa       | Vyžaduje údržbu a aktualizaci   | Zjednodušuje aktualizace aplikací. |
| Uživatelské prostředí      | Ovlivňuje čas přihlášení uživatele. Mezi stavem operačního systému, stavem aplikace a uživatelskými daty existuje hranice.  | Doručené aplikace se neliší od místně nainstalovaných aplikací. |

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o připojení aplikace MSIX, podívejte se na náš [Glosář](app-attach-glossary.md) a [Nejčastější dotazy](app-attach-faq.md). V opačném případě začněte s [nastavením připojit k aplikaci](app-attach.md).
