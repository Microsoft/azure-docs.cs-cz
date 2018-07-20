---
title: Další informace o dvoustupňovém ověřování v Azure MFA | Dokumentace Microsoftu
description: Co je Azure Multi-Factor Authentication, proč používat vícefaktorové ověřování a různé metody a verze, které jsou k dispozici.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d58d81d85dac7e5cd520b8d8a3fb5d91650e0cbe
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161601"
---
# <a name="what-is-azure-multi-factor-authentication"></a>Co je Azure Multi-Factor Authentication?

Dvoustupňové ověření je metoda ověřování, který vyžaduje více než jednu metodu ověřování a přidá do uživatelská přihlášení a transakce velmi důležitou druhou vrstvu zabezpečení. Funguje tak, že vyžaduje jakékoliv dvě nebo více z následujících metod ověřování:

* Něco víte (obvykle heslo)
* Něco, co máte (důvěryhodné zařízení, které není lehké duplikovat, jako je telefon)
* Něco, co že je (vaše biometrika)

<center>![Uživatelské jméno a heslo](./media/multi-factor-authentication/pword.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![certifikáty](./media/multi-factor-authentication/phone.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Inteligentní Phone](./media/multi-factor-authentication/hware.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![čipových karet](./media/multi-factor-authentication/smart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Virtuální čipové karty](./media/multi-factor-authentication/vsmart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![uživatelské jméno a heslo](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) je řešení dvoustupňového ověřování od Microsoftu. Azure MFA pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování prostřednictvím celé řady metod ověřování, včetně ověřování pomocí telefonních hovorů, textových zpráv nebo mobilních aplikací.

## <a name="why-use-azure-multi-factor-authentication"></a>Proč používat Azure Multi-Factor Authentication?
Dnes se více než kdy dřív uživatelé stále připojeni. Chytré telefony, tablety, přenosné počítače a počítače lidé mají několik možností ke svým účtům a aplikacím odkudkoli a kdykoli zůstali ve spojení.

Azure Multi-Factor Authentication je snadno použitelný, škálovatelné a spolehlivé řešení, které poskytuje druhé metody ověřování ochrana vašich uživatelů.

| ![Snadné použití](./media/multi-factor-authentication/simple.png) | ![Škálovatelné](./media/multi-factor-authentication/scalable.png) | ![Vždycky chráněné.](./media/multi-factor-authentication/protected.png) | ![Spolehlivost](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Snadné použití** |**Škálovatelné** |**Vždycky chráněné.** |**Spolehlivé** |

* **Snadno použitelné** – je jednoduché nastavit a začít používat Azure Multi-Factor Authentication. Zvláštní ochranu, která se dodává s ověřováním Azure Multi-Factor Authentication umožňuje uživatelům spravovat jejich vlastní zařízení. Co je nejlepší, v mnoha případech ho lze nastavit s několika jednoduchými kliknutími.
* **Škálovatelné** -využívá výkonný cloud Azure Multi-Factor Authentication a integruje se s místní AD a vlastních aplikací. Tato ochrana je i rozšířené velkého objemu, klíčových scénářích.
* **Vždy chránit** – ověřování Azure Multi-Factor Authentication poskytuje silné ověřování s použitím nejvyšší standardy.
* **Spolehlivé** – Microsoft zaručuje 99,9 % dostupnost služby Azure Multi-Factor Authentication. Služba se považuje za nedostupnou, pokud nemůže přijímat nebo zpracovávat žádosti o ověření pro dvoustupňové ověřování.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>Další postup

- Další informace o [jak funguje ověřování Azure Multi-Factor Authentication](concept-mfa-howitworks.md)

- Přečtěte si informace o různých [verze a využití metody pro ověřování Azure Multi-Factor Authentication](concept-mfa-licensing.md)
