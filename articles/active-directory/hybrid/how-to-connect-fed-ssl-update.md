---
title: Azure AD Connect – aktualizace certifikátu TLS/SSL pro farmu služby AD FS | Dokumenty společnosti Microsoft
description: Tento dokument podrobně popisuje kroky k aktualizaci certifikátu TLS/SSL farmy služby AD FS pomocí služby Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cc768162d98402fe52b52b2826a9dbf2840a581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331737"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualizace certifikátu TLS/SSL pro farmu služby AD FS (Active Directory Federation Services)

## <a name="overview"></a>Přehled
Tento článek popisuje, jak můžete pomocí služby Azure AD Connect aktualizovat certifikát TLS/SSL pro farmu služby AD FS (Active Directory Federation Services). Pomocí nástroje Azure AD Connect můžete snadno aktualizovat certifikát TLS/SSL pro farmu služby AD FS, i když vybraná metoda přihlášení uživatele není služba AD FS.

Celou operaci aktualizace certifikátu TLS/SSL pro farmu služby AD FS můžete provést na všech federačních serverech a serverech WAP (Federation Application Proxy) ve třech jednoduchých krocích:

![Tři kroky](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Další informace o certifikátech používaných službou AD FS naleznete v [tématu Principy certifikátů používaných službou AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Požadavky

* **Farma služby AD FS**: Ujistěte se, že vaše farma služby AD FS je založená na systému Windows Server 2012 R2 nebo novější.
* **Azure AD Connect**: Ujistěte se, že verze Azure AD Connect je 1.1.553.0 nebo vyšší. Budete používat úkol **Aktualizovat certifikát SSL**služby AD FS .

![Aktualizovat úlohu TLS](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Krok 1: Poskytnutí informací o farmě ad FS

Azure AD Connect se pokusí získat informace o farmě služby AD FS automaticky:
1. Dotazování na informace o farmě ze služby AD FS (Windows Server 2016 nebo novější).
2. Odkazování na informace z předchozích spuštění, které jsou uloženy místně s Azure AD Connect.

Seznam serverů, které jsou zobrazeny přidáním nebo odebráním serverů, můžete upravit tak, aby odrážel y aktuální konfiguraci farmy služby AD FS. Jakmile jsou k dispozici informace o serveru, Azure AD Connect zobrazí připojení a aktuální stav certifikátu TLS/SSL.

![Informace o serveru služby AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Pokud seznam obsahuje server, který již není součástí farmy služby AD FS, klepněte na **tlačítko Odebrat** a odstraňte server ze seznamu serverů ve farmě služby AD FS.

![Server offline v seznamu](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Odebrání serveru ze seznamu serverů pro farmu služby AD FS ve službě Azure AD Connect je místní operace a aktualizuje informace pro farmu služby AD FS, kterou Azure AD Connect udržuje místně. Azure AD Connect nezmění konfiguraci ve službě AD FS tak, aby odrážela změnu.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Krok 2: Poskytnutí nového certifikátu TLS/SSL

Po potvrzení informací o serverech farmy Služby AD FS požádá Služba Azure AD Connect o nový certifikát TLS/SSL. Poskytněte certifikát PFX chráněný heslem a pokračujte v instalaci.

![Certifikát TLS/SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Po zadání certifikátu Azure AD Connect prochází řadu předpokladů. Ověřte certifikát, abyste se ujistili, že certifikát je správný pro farmu služby AD FS:

-   Název subjektu/alternativní předmět certifikátu je buď stejný jako název federační služby, nebo se jedná o zástupný certifikát.
-   Certifikát je platný déle než 30 dní.
-   Řetěz důvěryhodnosti certifikátu je platný.
-   Certifikát je chráněn heslem.

## <a name="step-3-select-servers-for-the-update"></a>Krok 3: Výběr serverů pro aktualizaci

V dalším kroku vyberte servery, které potřebují aktualizovat certifikát TLS/SSL. Servery, které jsou offline, nelze pro aktualizaci vybrat.

![Výběr serverů, které chcete aktualizovat.](./media/how-to-connect-fed-ssl-update/selectservers.png)

Po dokončení konfigurace Azure AD Connect zobrazí zprávu, která označuje stav aktualizace a poskytuje možnost ověřit přihlášení služby AD FS.

![Dokončení konfigurace](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Nejčastější dotazy

* **Jaký by měl být název subjektu certifikátu pro nový certifikát AD FS TLS/SSL?**

    Azure AD Connect zkontroluje, jestli název subjektu nebo alternativní název předmětu certifikátu obsahuje název federační služby. Pokud je například název služby Federation Service fs.contoso.com, musí být fs.contoso.com název předmětu/alternativního předmětu.  Certifikáty se zástupnými symboly jsou také přijímány.

* **Proč jsem znovu požádán/a o pověření na stránce serveru WAP?**

    Pokud přihlašovací údaje, které zadáte pro připojení k serverům služby AD FS, nemají také oprávnění ke správě serverů WAP, požádá Azure AD Connect o pověření, která mají oprávnění správce na serverech WAP.

* **Server je zobrazen jako offline. Co mám dělat?**

    Azure AD Connect nemůže provádět žádné operace, pokud je server offline. Pokud je server součástí farmy služby AD FS, zkontrolujte připojení k serveru. Po vyřešení problému aktualizujte stav v průvodci stisknutím ikony aktualizace. Pokud byl server součástí farmy dříve, ale teď už neexistuje, klikněte na **Odebrat** a odstraňte ho ze seznamu serverů, které udržuje Azure AD Connect. Odebráním serveru ze seznamu ve službě Azure AD Connect nezměníte samotnou konfiguraci služby AD FS. Pokud používáte službu AD FS v systému Windows Server 2016 nebo novějším, server zůstane v nastavení konfigurace a znovu se zobrazí při příštím spuštění úlohy.

* **Mohu aktualizovat podmnožinu serverů farmy novým certifikátem TLS/SSL?**

    Ano. Chcete-li aktualizovat zbývající servery, můžete vždy znovu spustit **úlohu Aktualizovat certifikát SSL.** Na stránce **Vybrat servery pro aktualizaci certifikátu SSL** můžete seřadit seznam serverů v **datu vypršení platnosti ssl** a snadno přistupovat k serverům, které ještě nejsou aktualizovány.

* **Server byl odebrán v předchozím spuštění, ale server je stále zobrazen jako offline a uveden na stránce Servery služby AD FS. Proč je offline server stále tam i poté, co jsem ho odstranil?**

    Odebráním serveru ze seznamu ve službě Azure AD Connect jej v konfiguraci služby AD FS neodeberete. Azure AD Connect odkazuje na službu AD FS (Windows Server 2016 nebo vyšší) pro jakékoli informace o farmě. Pokud je server stále přítomen v konfiguraci služby AD FS, bude uveden zpět v seznamu.  

## <a name="next-steps"></a>Další kroky

- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
- [Správa a přizpůsobení služby AD Federování služby AD pomocí služby Azure AD Connect](how-to-connect-fed-management.md)

