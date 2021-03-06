---
title: Připojení k Microsoft Store klientovi – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí klienta Microsoft Store.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5662b32d8e2c9af457ded62b0302c2c64b31edea
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448011"
---
# <a name="connect-with-the-microsoft-store-client"></a>Připojení s klientem Microsoft Storu

>Platí pro: Windows 10.

K prostředkům virtuálních klientů Windows můžete přistupovat na zařízeních s Windows 10.

## <a name="install-the-microsoft-store-client"></a>Instalace klienta Microsoft Store

Klienta můžete nainstalovat pro aktuálního uživatele, který nevyžaduje oprávnění správce. Případně může správce nainstalovat a nakonfigurovat klienta tak, aby k němu měli přístup všichni uživatelé zařízení.

Po instalaci je možné klienta spustit z nabídky Start, a to tak, že vyhledáte vzdálenou plochu.

Chcete-li začít, [Stáhněte a nainstalujte klienta z Microsoft Store](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Přihlášení k odběru pracovního prostoru

Přihlaste se k odběru pracovního prostoru poskytnutého správcem, abyste získali seznam spravovaných prostředků, ke kterým máte přístup v počítači.

Přihlášení k odběru pracovního prostoru:

1. Na obrazovce Centrum připojení klepněte na **+ Přidat** a pak klepněte na **pracovní prostory**.
2. Do pole Adresa URL pracovního prostoru, které vám poskytl správce, zadejte adresu URL pracovního prostoru. Adresa URL pracovního prostoru může být buď adresa URL, nebo e-mailová adresa.
   
   - Pokud používáte adresu URL pracovního prostoru, použijte adresu URL, kterou vám správce poskytl.
   - Pokud se připojujete z virtuálního klienta Windows, použijte jednu z následujících adres URL v závislosti na tom, kterou verzi služby používáte:
       - Virtuální plocha Windows (Classic): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Virtuální počítač s Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. Klepněte na **Přihlásit se k odběru**.
4. Po zobrazení výzvy zadejte své přihlašovací údaje.
5. Po přihlášení k odběru by se měly pracovní prostory zobrazit v centru připojení.

Pracovní prostory lze přidat, změnit nebo odebrat na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat klienta Microsoft Store, najdete v článku [Začínáme s klientem Microsoft Store](/windows-server/remote/remote-desktop-services/clients/windows/).