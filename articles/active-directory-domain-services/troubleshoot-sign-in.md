---
title: Poradce při potížích s přihlášením ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit běžné problémy s přihlášením uživatelů a chyby ve službě Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612746"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Poradce při potížích s přihlášením k účtu se spravovanou doménou služby Azure AD Domain Services

Mezi nejčastější důvody pro uživatelský účet, který se nemůže přihlásit ke spravované doméně Azure AD DS, patří následující scénáře:

* [Účet ještě není synchronizován do služby Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS nemá hesla hashe, aby účet přihlásit.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Účet je uzamčen.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS nelze synchronizovat v přihlašovacích údajích pro účty, které jsou mimo klienta Azure AD. Externí uživatelé se nemůžou přihlásit ke spravované doméně Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Účet ještě není synchronizovaný do Služby Azure AD DS.

V závislosti na velikosti vašeho adresáře může chvíli trvat, než budou uživatelské účty a hash pověření k dispozici ve službě Azure AD DS. U velkých adresářů může tato počáteční jednosměrná synchronizace z Azure AD trvat několik hodin a až jeden den nebo dva. Ujistěte se, že před opakováním ověření počkejte dostatečně dlouho.

V hybridních prostředích, která uživatele Azure AD Connect synchronizují místní data adresáře do Azure AD, se ujistěte, že spustíte nejnovější verzi Azure AD Connect a [nakonfigurovali Azure AD Connect tak, aby po povolení Azure AD DS provedlúplnou synchronizaci.][azure-ad-connect-phs] Pokud zakážete Azure AD DS a znovu povolit, budete muset znovu podle těchto kroků.

Pokud máte i nadále problémy s účty, které nejsou synchronizovány prostřednictvím služby Azure AD Connect, restartujte službu Synchronizace Azure AD. Z počítače s nainstalovaným Službou Azure AD Connect otevřete okno příkazového řádku a spusťte následující příkazy:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS nemá hesla hashes

Azure AD negeneruje ani ukládat hesla hashe ve formátu, který je vyžadován pro ověřování NTLM nebo Kerberos, dokud nepovolíte Azure AD DS pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá žádné přihlašovací údaje hesla ve formě prostého textu. Azure AD proto nemůže automaticky generovat tyto hashe ntlm nebo kerberos hesla na základě existujících přihlašovacích údajů uživatelů.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hybridní prostředí s místní synchronizací

Pro hybridní prostředí pomocí Azure AD Connect k synchronizaci z místního prostředí služby AD DS můžete místně generovat a synchronizovat požadované hash hesla NTLM nebo Kerberos do služby Azure AD. Po vytvoření spravované domény Služby Azure AD DS [povolte synchronizaci hodnot hash hesel se službou Azure Active Directory Domain Services][azure-ad-connect-phs]. Bez dokončení tohoto kroku synchronizace hash hesla se nemůžete přihlásit k účtu pomocí služby Azure AD DS. Pokud zakážete Azure AD DS a znovu povolit, budete muset postupovat podle těchto kroků znovu.

Další informace naleznete v tématu [Jak funguje synchronizace hash hesel pro Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Prostředí pouze pro cloud bez místní synchronizace

Spravované domény Azure AD DS bez místní synchronizace, jenom účty ve službě Azure AD, také potřebujete generovat požadované hashe hesla NTLM nebo Kerberos. Pokud se účet pouze pro cloud nemůže přihlásit, byl proces změny hesla úspěšně dokončen pro účet po povolení Azure AD DS?

* **Ne, heslo nebylo změněno.**
    * [Změňte heslo pro účet][enable-user-accounts] pro generování požadovaných hesel hashe, pak počkejte 15 minut, než se pokusíte znovu přihlásit.
    * Pokud zakážete Azure AD DS a znovu povolit, každý účet musí podle pokynů znovu změnit své heslo a generovat požadované heslo hash.
* **Ano, heslo bylo změněno.**
    * Zkuste se přihlásit pomocí formátu *UPN,* například `driley@aaddscontoso.com`, namísto formátu *SAMAccountName,* jako je `AADDSCONTOSO\deeriley`.
    * *Název SAMAccountName* může být automaticky generován pro uživatele, jejichž předpona hlavního názvu uživatele je příliš dlouhá nebo je stejná jako jiný uživatel ve spravované doméně. Formát *UPN* je zaručeně jedinečný v rámci klienta Azure AD.

## <a name="the-account-is-locked-out"></a>Účet je uzamčen.

Uživatelský účet ve službě Azure AD DS je uzamčen, když byla splněna definovaná prahová hodnota pro neúspěšné pokusy o přihlášení. Toto chování uzamčení účtu je navržentak, aby vás chránil před opakovanými pokusy o přihlášení hrubou silou, které mohou naznačovat automatický digitální útok.

Ve výchozím nastavení, pokud je za 2 minuty 5 pokusů o chybné heslo, je účet uzamčen po dobu 30 minut.

Další informace a řešení problémů se uzamčením účtu najdete [v tématu Řešení problémů se uzamčením účtu ve službě Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Další kroky

Pokud máte pořád problémy s připojením virtuálního počítače ke spravované doméně Azure AD DS, [najděte nápovědu a otevřete lístek podpory pro Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
