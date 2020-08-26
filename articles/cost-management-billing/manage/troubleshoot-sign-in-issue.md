---
title: Řešení potíží s přihlášením k předplatnému Azure
description: Tato příručka vám pomůže vyřešit problémy s přihlášením na Azure Portal nebo do Centra účtů Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 3ee600cb72d06781f87c8f68640576afa50cea06
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686492"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Řešení potíží s přihlášením k předplatnému Azure

Tato příručka vám pomůže vyřešit problémy s přihlášením na Azure Portal nebo do Centra účtů Azure.

> [!NOTE]
> Pokud máte potíže s registrací nového účtu Azure, projděte si téma věnované [řešení potíží s registrací předplatného Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up).

## <a name="page-hangs-in-the-loading-status"></a>Stránka zamrzne ve stavu načítání

Pokud stránka v internetovém prohlížeči zamrzne, vyzkoušejte postupně všechny následující kroky, dokud se nedostanete na Azure Portal.

- Aktualizujte stránku.
- Použijte jiný internetový prohlížeč.
- Použijte privátní režim prohlížeče:

   - **Edge:** Otevřete **Nastavení** (tři tečky vedle vašeho profilového obrázku), vyberte **Nové okno InPrivate** a potom přejděte na [Azure Portal](https://portal.azure.com/) nebo do [Centra účtů Azure](https://account.azure.com/Subscriptions) a přihlaste se. 
   - **Chrome:** Zvolte režim **Incognito**.
   - **Safari:** Zvolte **Soubor** a potom **Nové privátní okno**.

- Vymažte mezipaměť a odstraňte internetové soubory cookie:

   - **Edge:** Otevřete **Nastavení** a vyberte **Ochrana osobních údajů a služby**. Postupujte podle kroků v části **Vymazat údaje o procházení**. Ověřte, že jsou zaškrtnuta zaškrtávací políčka **Historie procházení**, **Historie stahování** a **Obrázky a soubory v mezipaměti**, a potom vyberte **Odstranit**.
   - **Chrome:** Zvolte **Nastavení** a v části **Ochrana soukromí a zabezpečení** vyberte **Vymazat údaje o procházení**.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automaticky se přihlásíte jako jiný uživatel

K tomuto problému může dojít v případě, že v internetovém prohlížeči používáte více než jeden účet.

Při řešení tohoto problému zkuste použít některou z následujících metod:

- Vymažte mezipaměť a odstraňte internetové soubory cookie.

   - **Edge:** Otevřete **Nastavení** a vyberte **Ochrana osobních údajů a služby**. Postupujte podle kroků v části **Vymazat údaje o procházení**. Ověřte, že jsou zaškrtnuta zaškrtávací políčka **Historie procházení**, **Historie stahování**, **Soubory cookie** a **Obrázky a soubory v mezipaměti**, a potom vyberte **Odstranit**.
   - **Chrome:** Zvolte **Nastavení** a v části **Ochrana soukromí a zabezpečení** vyberte **Vymazat údaje o procházení**.
- Obnovte výchozí nastavení prohlížeče.
- Použijte privátní režim prohlížeče. 
   - **Edge:** Otevřete **Nastavení** (tři tečky vedle vašeho profilového obrázku), vyberte **Nové okno InPrivate** a potom přejděte na [Azure Portal](https://portal.azure.com/) nebo do [Centra účtů Azure](https://account.azure.com/Subscriptions) a přihlaste se. 
   - **Chrome:** Zvolte režim **Incognito**.
   - **Safari:** Zvolte **Soubor** a potom **Nové privátní okno**.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Můžu se přihlásit, ale zobrazí se chyba typu Nenašla se žádná předplatná

K tomuto problému dochází, pokud jste vybrali nesprávný adresář nebo pokud váš účet nemá dostatečná oprávnění.

**Scénář 1:** Při přihlášení k webu [Azure Portal](https://portal.azure.com/) se zobrazila chyba

Pokud chcete tento problém vyřešit:

- Vyberte vpravo nahoře na svůj účet a ujistěte se, že je vybraný správný adresář Azure.
- Pokud je vybraný správný adresář Azure, ale chybová zpráva se přesto zobrazuje, zajistěte, aby váš účet byl [přidán jako Vlastník](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator).

**Scénář 2:** Při přihlášení k [Centru účtů Azure](https://account.windowsazure.com/Subscriptions) se zobrazila chyba

Zkontrolujte, jestli účet, který jste použili, je účet správce účtu. Pokud chcete ověřit, kdo je správce účtu, postupujte takto:

1.  Přihlaste se k [zobrazení předplatných na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Vyberte předplatné, které chcete zkontrolovat, a potom vyberte **Nastavení**.
1.  Vyberte **Vlastnosti**. Správce účtu předplatného se zobrazí v poli **Správce účtu**.

## <a name="additional-help-resources"></a>Další pomocné materiály

Další články týkající se řešení potíží s fakturací a předplatnými Azure

- [Odmítnutá karta](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Problémy s registrací předplatného](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)
- [Nenalezena žádná předplatná](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Zákaz zobrazení firemních nákladů](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
- [Dokumentace k fakturaci Azure](https://docs.microsoft.com/azure/cost-management-billing/)

## <a name="contact-us-for-help"></a>Kontaktujte nás, abychom vám pomohli

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
