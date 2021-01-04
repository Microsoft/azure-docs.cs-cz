---
title: 'Azure AD Connect agent zřizování cloudu: Správa možností registru | Microsoft Docs'
description: Tento článek popisuje, jak spravovat možnosti registru v Azure AD Connect agenta zřizování cloudu.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: edb602e3d55ae07f49d5448283ae0d2b6da4b0cb
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694145"
---
# <a name="manage-agent-registry-options"></a>Spravovat možnosti registru agenta

Tato část popisuje možnosti registru, které můžete nastavit pro řízení chování při zpracování modulu runtime Azure AD Connect agenta zřizování. 

## <a name="configure-ldap-connection-timeout"></a>Konfigurace časového limitu připojení LDAP
Při provádění operací LDAP na konfigurovaných řadičích domény služby Active Directory ve výchozím nastavení používá agent zřizování výchozí hodnotu časového limitu připojení 30 sekund. Pokud váš řadič domény potřebuje reagovat déle, může se v souboru protokolu agenta zobrazit následující chybová zpráva: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

Pokud atribut hledání není indexován, operace vyhledávání LDAP mohou trvat déle. V prvním kroku, pokud se zobrazí výše uvedená chyba, nejprve ověřte, zda je atribut vyhledávání nebo vyhledávání [indexován](https://docs.microsoft.com/windows/win32/ad/indexed-attributes). Pokud jsou atributy vyhledávání indexované a chyba přetrvává, můžete zvýšit časový limit připojení LDAP pomocí následujících kroků: 

1. Přihlaste se jako správce na Windows serveru, na kterém běží agent zřizování Azure AD Connect.
1. Použití položky nabídky *Spustit* k otevření editoru registru (regedit.exe) 
1. Vyhledejte klíčovou složku **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Klikněte pravým tlačítkem a vyberte možnost "> hodnota řetězce".
1. Zadejte název: `LdapConnectionTimeoutInMilliseconds`
1. Dvakrát klikněte na **název hodnoty** a zadejte údaj hodnoty jako `60000` milisekundy.
    > [!div class="mx-imgBorder"]
    > ![Časový limit připojení LDAP](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Restartujte službu Azure AD Connect Provisioning z konzoly *služby* .
1. Pokud jste nasadili více zřizovacích agentů, použijte tuto změnu v registru na všechny agenty pro zajištění konzistence. 

## <a name="configure-referral-chasing"></a>Konfigurace dohledávání odkazů
Ve výchozím nastavení Azure AD Connect agent zřizování nepodstatou [odkazy](https://docs.microsoft.com/windows/win32/ad/referrals). Možná budete chtít povolit referenční dohledávání, aby podporovaly určité scénáře zajišťování pro příchozí personál, jako například: 
* Kontrola jedinečnosti hlavního názvu uživatele (UPN) napříč více doménami
* Překládání odkazů mezi doménovými manažery

K zapnutí referenčního dohledávání použijte následující postup:

1. Přihlaste se jako správce na Windows serveru, na kterém běží agent zřizování Azure AD Connect.
1. Použití položky nabídky *Spustit* k otevření editoru registru (regedit.exe) 
1. Vyhledejte klíčovou složku **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Klikněte pravým tlačítkem a vyberte možnost "> hodnota řetězce".
1. Zadejte název: `ReferralChasingOptions`
1. Dvakrát klikněte na **název hodnoty** a zadejte údaj hodnoty `96` . Tato hodnota odpovídá konstantní hodnotě pro `ReferralChasingOptions.All` a určuje, zda bude následován podstrom i odkaz na základní úrovni. 
    > [!div class="mx-imgBorder"]
    > ![Dohledávání odkazů](media/how-to-manage-registry-options/referral-chasing.png)
1. Restartujte službu Azure AD Connect Provisioning z konzoly *služby* .
1. Pokud jste nasadili více zřizovacích agentů, použijte tuto změnu v registru na všechny agenty pro zajištění konzistence.

## <a name="skip-gmsa-configuration"></a>Přeskočit konfiguraci GMSA
U agenta verze 1.1.281.0 + ve výchozím nastavení se při spuštění Průvodce konfigurací agenta zobrazí výzva k nastavení [skupinový účet spravované služby (gMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview). Instalační program Průvodce GMSA se používá za běhu pro všechny operace synchronizace a zřizování. 

Pokud provádíte upgrade z předchozí verze agenta a nastavili jste vlastní účet služby s delegovanými oprávněními na úrovni organizační jednotky, které jsou specifické pro vaši topologii služby Active Directory, můžete pro tuto změnu přeskočit nebo odložit GMSA konfiguraci a plán. 

> [!NOTE]
> Tento návod se vztahuje konkrétně na zákazníky, kteří nakonfigurovali příchozí zřizování HR (Workday/SuccessFactors) s verzemi agentů před 1.1.281.0 a které nastavily vlastní účet služby pro operace agenta. V dlouhodobém běhu doporučujeme přepnout na GMSA jako osvědčený postup.  

V tomto scénáři můžete upgradovat binární soubory agenta a přeskočit konfiguraci GMSA pomocí následujících kroků: 

1. Přihlaste se jako správce na Windows serveru, na kterém běží agent zřizování Azure AD Connect.
1. Spusťte instalační program agenta a nainstalujte nové binární soubory agenta. Zavřete Průvodce konfigurací agenta, který se po úspěšném dokončení instalace automaticky otevře. 
1. Použití položky nabídky *Spustit* k otevření editoru registru (regedit.exe) 
1. Vyhledejte klíčovou složku **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Klikněte pravým tlačítkem a vyberte "New-> Value DWORD"
1. Zadejte název: `UseCredentials`
1. Dvakrát klikněte na **název hodnoty** a zadejte údaj hodnoty `1` .  
    > [!div class="mx-imgBorder"]
    > ![Použít přihlašovací údaje](media/how-to-manage-registry-options/use-credentials.png)
1. Restartujte službu Azure AD Connect Provisioning z konzoly *služby* .
1. Pokud jste nasadili více zřizovacích agentů, použijte tuto změnu v registru na všechny agenty pro zajištění konzistence.
1. Na ploše se zkráceným vyjmutím spusťte Průvodce konfigurací agenta. Průvodce přeskočí konfiguraci GMSA. 


> [!NOTE]
> Možnosti registru si můžete ověřit tak, že povolíte [podrobné protokolování](how-to-troubleshoot.md#log-files). Protokoly generované během spuštění agenta zobrazí konfigurační hodnoty vydané z registru. 

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

