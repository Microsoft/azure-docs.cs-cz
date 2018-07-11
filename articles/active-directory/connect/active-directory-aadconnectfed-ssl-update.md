---
title: Azure AD Connect – aktualizace certifikátu SSL pro farmu služby AD FS | Dokumentace Microsoftu
description: Tento dokument podrobně popisuje kroky a aktualizujete certifikát SSL farmy služby AD FS pomocí služby Azure AD Connect.
services: active-directory
manager: mtillman
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.component: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.openlocfilehash: 0eeb3f7d54617ff060481795bcdaa8b54e36dfa8
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917648"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualizace certifikátu SSL pro farmu služby Active Directory Federation Services (AD FS)

## <a name="overview"></a>Přehled
Tento článek popisuje, jak můžete pomocí služby Azure AD Connect k aktualizaci certifikátu SSL pro farmu služby Active Directory Federation Services (AD FS). Nástroj Azure AD Connect můžete snadno aktualizovat certifikát SSL pro farmu služby AD FS, i když není uživatel přihlašovací metoda vybrané služby AD FS.

Můžete provést i celou operaci aktualizace certifikátu SSL pro farmu služby AD FS na všech federačních a servery Proxy webových aplikací (WAP) ve třech jednoduchých krocích:

![Tři kroky](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Další informace o certifikátech, které používají službu AD FS najdete v tématu [Principy certifikátů používaných službou AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Požadavky

* **Farma služby AD FS**: Ujistěte se, že vaše farma služby AD FS je založené na Windows Server 2012 R2 nebo novější.
* **Azure AD Connect**: Ujistěte se, že verze služby Azure AD Connect 1.1.553.0 nebo vyšší. Budete používat úlohy **aktualizace AD certifikát SSL služby FS**.

![Úloha aktualizace SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Krok 1: Zadání informací o farmě služby AD FS

Azure AD Connect se pokusí získat informace o farmě služby AD FS automaticky:
1. Dotaz na informace o farmě ze služby AD FS (Windows Server 2016 nebo novější).
2. Odkazování na informace z předchozích spuštění, která se uloží místně pomocí služby Azure AD Connect.

Můžete upravit seznam serverů, které jsou zobrazeny přidáním nebo odebráním servery tak, aby odrážel aktuální konfiguraci farmy služby AD FS. Jakmile je k dispozici informace o serveru, Azure AD Connect zobrazí připojení a aktuální stav certifikátu SSL.

![Informace o serveru AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Pokud seznam obsahuje server, který už není součástí farmy služby AD FS, klikněte na tlačítko **odebrat** odstranit server ze seznamu serverů ve farmě služby AD FS.

![Offline server v seznamu](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Odebrání serveru ze seznamu serverů pro farmu služby AD FS v Azure AD Connect je místní operace a aktualizuje informace pro farmu služby AD FS, který Azure AD Connect udržuje místně. Azure AD Connect nezmění konfigurace ve službě AD FS tak, aby odrážely změny.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Krok 2: Zadání nového certifikátu protokolu SSL

Po ověření, že informace o servery ve farmě služby AD FS, Azure AD Connect požádá o nový certifikát protokolu SSL. Zadejte certifikát PFX chráněný heslem pokračujte v instalaci.

![Certifikát SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

Po zadání certifikátu služby Azure AD Connect prochází řadu požadavky. Ověřte certifikát k ověření, že certifikát je správná pro farmu služby AD FS:

-   Předmět název nebo alternativní název subjektu certifikátu je buď stejná jako název služby FS, nebo je certifikát se zástupným znakem.
-   Certifikát je platný po dobu více než 30 dnů.
-   Řetěz certifikátů je platný.
-   Certifikát je chráněn heslem.

## <a name="step-3-select-servers-for-the-update"></a>Krok 3: Vyberte servery pro aktualizaci

V dalším kroku vyberte servery, které musí mít certifikát SSL aktualizovat. Pro aktualizaci nelze vybrat servery, které jsou offline.

![Vyberte servery pro aktualizaci](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Po dokončení konfigurace služby Azure AD Connect se zobrazí zpráva, která označuje stav aktualizace a nabízí možnost ověření přihlášení služby AD FS.

![Dokončení konfigurace](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Nejčastější dotazy

* **Co by měl být název subjektu certifikátu pro nový certifikát SSL služby AD FS?**

    Azure AD Connect zkontroluje, jestli subjektu název nebo alternativní název subjektu certifikátu obsahuje název federační služby. Například pokud je název vaší služby federačního serveru fs.contoso.com, předmět název nebo alternativní název subjektu musí být fs.contoso.com.  Přijímat zástupné certifikáty jsou také.

* **Proč se zobrazuje výzva k zadání pověření znovu na stránce server WAP?**

    Pokud přihlašovací údaje, které zadáte pro připojování k serverům AD FS také nemají oprávnění ke správě serverů WAP, pak vyzve k zadání přihlašovacích údajů, které mají oprávnění správce na serverech WAP Azure AD Connect.

* **Na serveru se zobrazí jako offline. Co bych měl/a dělat?**

    Azure AD Connect jakékoli operaci nelze provést, pokud je server offline. Pokud server je součástí farmy služby AD FS, zkontrolujte připojení k serveru. Po vyřešení problému stiskněte ikonu aktualizace pro aktualizaci stavu v průvodci. Pokud byl server součástí farmy dříve, ale nyní již neexistuje, klikněte na tlačítko **odebrat** udržuje ho odstranit ze seznamu serverů této služby Azure AD Connect. Odebrat server ze seznamu ve službě Azure AD Connect není změnit konfiguraci služby AD FS, samotného. Pokud používáte službu AD FS ve Windows serveru 2016 nebo novější, zůstane serveru v nastavení konfigurace a zobrazí se při příštím spuštění úlohy.

* **Můžete aktualizovat podmnožinu serverů své farmy pomocí nového certifikátu protokolu SSL?**

    Ano. Můžete kdykoli spustit úlohu **aktualizovat certifikát SSL** aktualizovat zbývající servery. Na **aktualizaci certifikátu vyberte servery pro protokol SSL** stránky, můžete seřadit seznam serverů na **datum vypršení platnosti protokolu SSL** pro snadný přístup k servery, které ještě nejsou aktualizovány.

* **Po odebrání serveru v předchozím spuštění, ale je stále se zobrazuje jako offline a jsou uvedeny na stránce servery služby AD FS. Proč po offline server stále existuje i po odebrání?**

    Odebrat server ze seznamu ve službě Azure AD Connect, neodebere se v konfiguraci služby AD FS. Azure AD Connect odkazuje na službu AD FS (Windows Server 2016 nebo novější) pro všechny informace o farmě. Pokud je server stále k dispozici v konfiguraci služby AD FS, zobrazí se zpět v seznamu.  

## <a name="next-steps"></a>Další postup

- [Azure AD Connect a federace](active-directory-aadconnectfed-whatis.md)
- [Active Directory Federation Services management a přizpůsobení službou Azure AD Connect](active-directory-aadconnect-federation-management.md)
