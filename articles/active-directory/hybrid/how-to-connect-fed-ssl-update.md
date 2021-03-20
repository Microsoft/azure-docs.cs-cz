---
title: Azure AD Connect – aktualizace certifikátu TLS/SSL pro AD FS farmu | Microsoft Docs
description: Tento dokument podrobně popisuje postup aktualizace certifikátu TLS/SSL AD FS farmy pomocí Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279903"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualizace certifikátu TLS/SSL pro farmu Active Directory Federation Services (AD FS) (AD FS)

## <a name="overview"></a>Přehled
Tento článek popisuje, jak můžete pomocí Azure AD Connect aktualizovat certifikát TLS/SSL pro farmu Active Directory Federation Services (AD FS) (AD FS). Pomocí nástroje Azure AD Connect můžete snadno aktualizovat certifikát TLS/SSL pro AD FS farmu i v případě, že vybraná metoda přihlašování uživatele není AD FS.

Celou operaci aktualizace certifikátu TLS/SSL pro AD FSovou farmu napříč všemi federačními servery a proxy webových aplikací (WAP) můžete provádět ve třech jednoduchých krocích:

![Tři kroky](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Další informace o certifikátech, které používá AD FS, najdete v tématu [Principy certifikátů používaných v AD FS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11)).

## <a name="prerequisites"></a>Předpoklady

* **AD FS farma**: Ujistěte se, že vaše AD FS farma je Windows Server 2012 R2 nebo novější.
* **Azure AD Connect**: Ujistěte se, že je verze Azure AD Connect 1.1.553.0 nebo vyšší. Použijete **aktualizaci úlohy AD FS certifikát SSL**.

![Aktualizovat úlohu TLS](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Krok 1: zadání informací o AD FS farmě

Azure AD Connect se automaticky pokusí získat informace o AD FS farmě:
1. Dotazování se na informace o farmě z AD FS (Windows Server 2016 nebo novější).
2. Odkazování na informace z předchozích spuštění, které jsou uloženy místně pomocí Azure AD Connect.

Můžete upravit seznam serverů, které jsou zobrazeny přidáním nebo odebráním serverů, aby odrážely aktuální konfiguraci AD FS farmy. Jakmile jsou informace o serveru k dispozici, Azure AD Connect zobrazí připojení a aktuální stav certifikátu TLS/SSL.

![Informace o AD FSm serveru](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Pokud seznam obsahuje server, který už není součástí AD FS farmy, klikněte na **Odebrat** , aby se server odstranil ze seznamu serverů ve vaší AD FS farmě.

![Offline server v seznamu](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Odebrání serveru ze seznamu serverů pro AD FSovou farmu v Azure AD Connect je místní operace a aktualizuje informace pro AD FS farmu, která Azure AD Connect udržuje místně. Azure AD Connect nemění konfiguraci AD FS tak, aby odrážela změnu.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Krok 2: zadání nového certifikátu TLS/SSL

Po potvrzení informací o AD FS serverech farmy Azure AD Connect požádá o nový certifikát TLS/SSL. Pokud chcete pokračovat v instalaci, zadejte certifikát PFX chráněný heslem.

![Certifikát TLS/SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Po zadání certifikátu se Azure AD Connect projde řadou požadavků. Ověřte certifikát, abyste měli jistotu, že je certifikát pro AD FS farmu správný:

-   Název předmětu nebo alternativní název subjektu pro certifikát je buď stejný jako název služby FS (Federation Service), nebo se jedná o certifikát se zástupným znakem.
-   Certifikát je platný po dobu delší než 30 dní.
-   Řetěz důvěryhodných certifikátů je platný.
-   Certifikát je chráněný heslem.

## <a name="step-3-select-servers-for-the-update"></a>Krok 3: Vyberte servery pro aktualizaci

V dalším kroku vyberte servery, které vyžadují aktualizaci certifikátu TLS/SSL. Pro aktualizaci nelze vybrat servery, které jsou v režimu offline.

![Vyberte servery, které chcete aktualizovat.](./media/how-to-connect-fed-ssl-update/selectservers.png)

Po dokončení konfigurace Azure AD Connect zobrazí zprávu, která indikuje stav aktualizace, a nabídne možnost ověřit AD FS přihlášení.

![Dokončení konfigurace](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Nejčastější dotazy

* **Jaký by měl být název subjektu certifikátu pro nový AD FS certifikát TLS/SSL?**

    Azure AD Connect zkontroluje, jestli název předmětu nebo alternativní název subjektu certifikátu obsahuje název služby FS (Federation Service). Například pokud je název federační služby fs.contoso.com, musí být název předmětu nebo alternativní název subjektu fs.contoso.com.  Jsou také přijaty certifikáty se zástupnými znaky.

* **Proč se na stránce serveru WAP znovu zobrazí výzva k zadání přihlašovacích údajů?**

    Pokud přihlašovací údaje, které zadáte pro připojení k serverům AD FS, nemají také oprávnění ke správě serverů WAP, Azure AD Connect požádá o přihlašovací údaje, které mají oprávnění správce na serverech WAP.

* **Server se zobrazuje v režimu offline. Co mám dělat?**

    Azure AD Connect nemůže provádět žádné operace, pokud je server offline. Pokud je Server součástí farmy AD FS, zkontrolujte připojení k serveru. Po vyřešení problému stiskněte ikonu aktualizovat a aktualizujte stav průvodce. Pokud byl Server součástí farmy dříve, ale nyní již neexistuje, klikněte na tlačítko **Odebrat** a odstraňte jej ze seznamu serverů, které Azure AD Connect zachovány. Odebrání serveru ze seznamu v Azure AD Connect nemění vlastní konfiguraci AD FS. Pokud používáte AD FS ve Windows serveru 2016 nebo novějším, server zůstane v nastavení konfigurace a při příštím spuštění úlohy se zobrazí znovu.

* **Můžu aktualizovat podmnožinu serverů farmy pomocí nového certifikátu TLS/SSL?**

    Ano. Vždy můžete znovu spustit **certifikát SSL aktualizace** a aktualizovat zbývající servery. Na stránce **Vybrat servery pro aktualizaci certifikátu SSL** můžete seřadit seznam serverů v **datu vypršení platnosti SSL** a snadno tak přistupovat k serverům, které ještě nejsou aktualizované.

* **Odebral (a) jsem server v předchozím běhu, ale pořád se zobrazuje v režimu offline a je uvedený na stránce AD FS servery. Proč je offline server stále i po jeho odebrání?**

    Odebrání serveru ze seznamu v Azure AD Connect ho neodeberete v konfiguraci AD FS. Azure AD Connect odkazuje na AD FS (Windows Server 2016 nebo vyšší) pro všechny informace o farmě. Pokud je server stále přítomen v konfiguraci AD FS, bude uveden v seznamu zpět.  

## <a name="next-steps"></a>Další kroky

- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
- [Active Directory Federation Services (AD FS) Správa a přizpůsobení pomocí Azure AD Connect](how-to-connect-fed-management.md)