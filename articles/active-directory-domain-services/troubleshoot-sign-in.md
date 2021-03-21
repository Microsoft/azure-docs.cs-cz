---
title: Řešení potíží s přihlašováním v Azure AD Domain Services | Microsoft Docs
description: Naučte se řešit běžné problémy s přihlašováním uživatelů a chyby v Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618565"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Řešení potíží s přihlašováním k účtu pomocí spravované domény Azure Active Directory Domain Services

Nejběžnějšími důvody pro uživatelský účet, který se nemůže přihlásit ke spravované doméně služby Azure Active Directory Domain Services (Azure služba AD DS), jsou tyto scénáře:

* [Účet zatím není synchronizovaný do Azure služba AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure služba AD DS nemá hodnoty hash hesla, aby se mohl přihlásit k účtu.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Účet je uzamčený.](#the-account-is-locked-out)

> [!TIP]
> Azure služba AD DS se nemůže synchronizovat s přihlašovacími údaji pro účty, které jsou externí pro tenanta Azure AD. Externí uživatelé se nemůžou přihlásit ke spravované doméně Azure služba AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Účet se zatím nesynchronizoval do Azure služba AD DS

V závislosti na velikosti adresáře může trvat nějakou dobu, než se uživatelské účty a hodnoty hash přihlašovacích údajů zpřístupní ve spravované doméně. U rozsáhlých adresářů může tato počáteční synchronizace z Azure AD trvat několik hodin a až jeden den nebo dvě. Před opakovaným pokusem o ověření se ujistěte, že jste čekali dostatečně dlouho.

Pro hybridní prostředí, která uživatel Azure AD Connect k synchronizaci místních adresářových dat do Azure AD, se ujistěte, že používáte nejnovější verzi Azure AD Connect a máte [nakonfigurovanou Azure AD Connect k provedení úplné synchronizace po povolení služba AD DS Azure][azure-ad-connect-phs]. Pokud zakážete Azure služba AD DS a pak znovu povolíte, budete muset postupovat znovu.

Pokud budete mít nadále problémy s účty, které se nesynchronizují prostřednictvím Azure AD Connect, restartujte službu Azure AD Sync. Z počítače s nainstalovanou Azure AD Connect otevřete okno příkazového řádku a spusťte následující příkazy:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure služba AD DS nemá hodnoty hash hesel.

Azure AD negeneruje nebo ukládá hodnoty hash hesel ve formátu, který je vyžadován pro ověřování protokolem NTLM nebo Kerberos, dokud nepovolíte služba AD DS Azure pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá přihlašovací údaje hesla ve formě nešifrovaných textů. Proto služba Azure AD nemůže automaticky generovat tyto hodnoty hash hesla NTLM nebo Kerberos na základě stávajících přihlašovacích údajů uživatelů.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hybridní prostředí s místní synchronizací

Pro hybridní prostředí, která používají Azure AD Connect k synchronizaci z místního prostředí služba AD DS můžete v Azure AD místně vygenerovat a synchronizovat požadované hodnoty hash hesla protokolu NTLM nebo Kerberos. Po vytvoření spravované domény [Povolte synchronizaci hodnot hash hesel na Azure Active Directory Domain Services][azure-ad-connect-phs]. Bez dokončení kroku synchronizace hodnoty hash hesla se nemůžete přihlásit k účtu pomocí spravované domény. Pokud zakážete Azure služba AD DS a pak znovu povolíte, budete muset postupovat znovu.

Další informace najdete v tématu [Jak funguje synchronizace hodnot hash hesel pro Azure služba AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Pouze cloudová prostředí bez místní synchronizace

Spravované domény bez místní synchronizace: pro účty ve službě Azure AD musí taky vygenerovat požadované hodnoty hash hesla protokolu NTLM nebo Kerberos. Pokud se účet jenom pro Cloud nemůže přihlásit, má po povolení Azure služba AD DS úspěšné dokončení procesu změny hesla pro účet?

* **Ne, heslo se nezměnilo.**
    * [Změňte heslo pro účet][enable-user-accounts] , abyste vygenerovali požadované hodnoty hash hesla, a pak počkejte 15 minut, než se znovu pokusíte o přihlášení.
    * Pokud zakážete Azure služba AD DS a pak znovu povolíte, každý účet musí znovu postupovat podle kroků a změnit heslo a vygenerovat požadované hodnoty hash hesla.
* **Ano, heslo bylo změněno.**
    * Pokuste se přihlásit pomocí *hlavního názvu uživatele (UPN)* , jako `driley@aaddscontoso.com` je třeba místo formátu *sAMAccountName* `AADDSCONTOSO\deeriley` .
    * *SAMAccountName* se může automaticky vygenerovat pro uživatele, jejichž předpona hlavního názvu uživatele je příliš dlouhá nebo je stejná jako jiný uživatel ve spravované doméně. Formát *UPN* je v rámci TENANTA Azure AD zaručený jako jedinečný.

## <a name="the-account-is-locked-out"></a>Účet je uzamčený.

Uživatelský účet ve spravované doméně je uzamčen, pokud byla splněna definovaná prahová hodnota pro neúspěšné pokusy o přihlášení. Toto chování uzamčení účtu je navrženo tak, aby vás chránilo před opakovanými pokusy o přihlašování hrubou silou, které můžou znamenat automatizovaný digitální útok.

Ve výchozím nastavení platí, že pokud se v 2 minutách vyskytne 5 špatný počet pokusů o zadání hesla, účet se zablokuje na 30 minut.

Další informace a řešení problémů s uzamčením účtu najdete v tématu [řešení problémů s uzamčením účtu v Azure služba AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Další kroky

Pokud stále máte problémy s připojením k VIRTUÁLNÍmu počítači ke spravované doméně, [Najděte nápovědu a otevřete lístek podpory pro Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md