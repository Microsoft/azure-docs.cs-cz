---
title: Připojení k virtuální ploše Preview Windows z Windows 10 nebo Windows 7 – Azure
description: Jak se připojit k verzi Preview virtuální plochy Windows z Windows 10 nebo Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145994"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Připojení z Windows 10 nebo Windows 7

> To platí pro: Windows 7 a Windows 10.

Je k dispozici ke stažení klienta, která poskytuje přístup k prostředkům Windows Virtual Desktop Preview ze zařízení s Windows 7 a Windows 10.

> [!IMPORTANT]
> Nepoužívejte **aplikacím RemoteApp a vzdálené ploše připojení (RADC)** nebo **připojení vzdálené plochy (MSTSC)** pro přístup k prostředkům virtuální plochy Windows, protože virtuální plochy Windows nepodporuje buď klienta.

## <a name="install-the-client"></a>Instalace klienta

[Stáhněte si](https://go.microsoft.com/fwlink/?linkid=2068602) a instalaci klienta do vašeho místního počítače. Instalace vyžaduje oprávnění správce.

## <a name="subscribe-to-a-feed"></a>Přihlášení odběru informačního kanálu

Získání seznamu spravované prostředky, které jsou k dispozici pro vás prostřednictvím přihlášení odběru informačního kanálu zadané váš správce. Přihlášení k odběru zpřístupní prostředky ve vašem místním počítači.

K odběru informačního kanálu:

1. Spuštění klienta z seznam všech aplikací, hledejte **vzdálené plochy**.
1. Vyberte **přihlásit k odběru** na hlavní stránce pro připojení ke službě a načíst vaše prostředky.
1. **Přihlaste se** s vaším uživatelským účtem po zobrazení výzvy.

Po úspěšném ověření, měli byste vidět seznam pro vás dostupné materiály.

Prostředky můžete spustit pomocí jedné ze dvou způsobů.

- Na hlavní stránce klienta dvakrát klikněte na prostředek se ho spustit.
- Spusťte prostředek běžným způsobem další aplikace v nabídce Start.
  - Můžete také vyhledat aplikaci v panelu vyhledávání.

Jakmile se předplatné služby informačního kanálu, obsah informačního kanálu se aktualizuje automaticky v pravidelných intervalech. Prostředky mohou přidávání, změny a odebírání na základě změn provedená správcem.

## <a name="view-the-details-of-a-feed"></a>Zobrazit podrobnosti o informačního kanálu

Po přihlášení k odběru, můžete zobrazit další informace o kanálu díky přístupu do panelu podrobností.

1. Na hlavní stránce klienta, vyberte tři tečky ( **...** ) napravo od názvu informačního kanálu.
1. Z rozevírací nabídky vyberte **podrobnosti**.
1. Panel podrobností se zobrazí na pravé straně klienta.

Panel podrobnosti obsahuje užitečné informace o informačního kanálu:

- Adresa URL a uživatelské jméno používané pro přihlášení odběru
- Počet aplikací a stolní počítače
- Datum a čas poslední aktualizace
- Stav poslední aktualizace

V případě potřeby můžete spustit ruční aktualizaci tak, že vyberete na **aktualizovat**.

## <a name="unsubscribe-from-a-feed"></a>Odhlásit z informačního kanálu

V této části se dozvíte, jak k odhlášení odběru z informačního kanálu. Může odhlásit odběr znovu s jiným účtem nebo odebrat zdroje ze systému.

1. Na hlavní stránce klienta, vyberte tři tečky ( **...** ) napravo od názvu informačního kanálu.
1. Z rozevírací nabídky vyberte **Unsubscribe**.
1. Zkontrolujte a vyberte **pokračovat** z tohoto dialogového okna.

## <a name="update-the-client"></a>Aktualizace klienta

Pokud je dostupná nová verze klienta, budete upozorněni na klienta a Centrum akcí Windows. Vyberte oznámení k zahájení procesu aktualizace.
