---
title: Publikování aplikací pomocí proxy aplikace služby Azure AD | Dokumentace Microsoftu
description: Publikování místních aplikací do cloudu pomocí Proxy aplikace Azure AD na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1224642bb7e6fc0c51b3f839a78449132db5b4bb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364253"
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publikování aplikací pomocí proxy aplikace služby Azure AD

Proxy aplikací služby Azure Active Directory (AD) umožňuje podporu vzdálených pracovních procesů publikováním místních aplikací, aby byly přístupné přes internet. Můžete publikovat tyto aplikace na webu Azure portal a poskytuje zabezpečený vzdálený přístup mimo vaši síť.

Tento článek vás provede kroky k publikování aplikace v místním pomocí Proxy aplikací. Po dokončení tohoto článku, uživatelé budou moct vzdálený přístup k aplikaci. A budete připraveni ke konfiguraci dalších funkcí pro aplikaci, jako je jednotné přihlašování, individuální informace a požadavky na zabezpečení.

Pokud službou Proxy aplikací teprve začínáte, projděte si další informace o této funkci s článkem [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, máte již nainstalována a registrována konektor. Pokud je stále potřeba provést tyto kroky, přečtěte si téma [začít pracovat s Proxy aplikace a nainstalujte konektor](application-proxy-enable.md).

## <a name="publish-an-on-premises-app-for-remote-access"></a>Publikování aplikace v místním pro vzdálený přístup

Použijte následující postup publikování aplikací pomocí Proxy aplikace. Pokud jste již nebyly stáhli a nakonfigurovat konektor pro vaši organizaci, přejděte na [začít pracovat s Proxy aplikace a nainstalujte konektor](application-proxy-enable.md) první a pak aplikaci publikujte.

> [!TIP]
> Pokud testujete aplikaci Proxy poprvé, vyberte aplikaci, která je nastavena pro ověřování pomocí hesla. Proxy aplikací podporuje jiné typy ověřování, ale jsou založené na heslech aplikací nejjednodušší ke zprovoznění a rychle. 

1. Přihlaste se jako správce [webu Azure portal](https://portal.azure.com/).
2. Vyberte **Azure Active Directory** > **podnikové aplikace** > **novou aplikaci**.

  ![Přidat podniková aplikace](./media/application-proxy-publish-azure-portal/add-app.png)

3. Vyberte **všechny**a pak vyberte **On-premises application**.  

  ![Přidat svoji vlastní aplikaci](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Zadejte následující informace o vaší aplikaci:

   - **Název**: název aplikace, které se zobrazí na přístupovém panelu a na webu Azure Portal. 

   - **Interní adresa URL**: adresa URL, který používáte pro přístup k aplikaci uvnitř vaší privátní sítě. Můžete zadat konkrétní cestu na beck-endovém serveru, kterou chcete publikovat, zatímco zbytek serveru publikovaný nebude. Tímto způsobem můžete publikovat různé stránky na stejném serveru jako různé aplikace a použít každý z nich vlastní název a pravidla přístupu.

     > [!TIP]
     > Pokud publikujete cestu, ujistěte se, že zahrnuje všechny nezbytné obrázky, skripty a šablony stylů pro vaši aplikaci. Například, pokud je vaše aplikace na https://yourapp/app a používá obrázky nacházející se v https://yourapp/media, pak by měli zveřejnit https://yourapp/ jako cestu. Toto interní adresa URL nemusí být na úvodní stránku, kterou uživatelé uvidí. Další informace najdete v tématu [nastavit vlastní domovskou stránku pro publikované aplikace](application-proxy-configure-custom-home-page.md).

   - **Externí adresa URL**: adresa vaši uživatelé budou moct pro přístup k aplikaci mimo vaši síť. Pokud už nechcete používat výchozí doménu Proxy aplikací, přečtěte si informace o [vlastních domén v Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
   - **Předběžné ověření**: jak Proxy aplikace ověřuje uživatele, než jim poskytne přístup k aplikaci. 

     - Azure Active Directory: Proxy aplikace přesměruje uživatele na stránku pro přihlášení ke službě Azure AD, která ověří jejich oprávnění k adresáři a aplikaci. Doporučujeme tuto možnost jako výchozí, takže můžete využít výhod funkcí zabezpečení Azure AD, jako je podmíněný přístup a ověřování službou Multi-Factor Authentication.
     - Průchod: Uživatelé nemají k ověřování na základě Azure Active Directory pro přístup k aplikaci. Stále můžete nastavit požadavky na ověřování na back-endu.
   - **Skupina konektorů**: konektory zpracování vzdálený přístup do vaší aplikace a usnadňují uspořádání konektory a aplikace podle oblasti, sítě nebo účel skupiny konektoru. Pokud nemáte žádné skupiny konektorů nemáte ještě vytvořený, vaše aplikace se přiřadí **výchozí**.

>[!NOTE]
>Pokud vaše aplikace používá objekty websocket pro připojení, zkontrolujte, že máte verze konektoru 1.5.612.0 nebo vyšší s podporou protokolu websocket a přiřazená skupina konektorů využívá jenom tyto konektory.

   ![Konfigurace aplikace](./media/application-proxy-publish-azure-portal/configure-app.png)
5. V případě potřeby nakonfigurujte další nastavení. Pro většinu aplikací byste měli mít tato nastavení na jejich výchozí stavy. 
   - **Časový limit pro back-endu aplikace**: nastavte tuto hodnotu na **dlouhé** pouze v případě, že vaše aplikace se ověřili a připojili. 
   - **Překládat adresy URL v hlavičkách**: ponechte tuto hodnotu jako **Ano** Pokud vaše aplikace vyžaduje hlavičku původního hostitele v žádosti o ověření.
   - **Překládat adresy URL v těle žádosti**: ponechte tuto hodnotu jako **ne** Pokud nemáte pevně zakódované HTML odkazy na další místní aplikace a nebude se používat vlastní domény. Další informace najdete v tématu [propojit překlad textů pomocí Proxy aplikací](application-proxy-configure-hard-coded-link-translation.md).
   
   ![Konfigurace aplikace](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Vyberte **Přidat**.


## <a name="add-a-test-user"></a>Přidání testovacího uživatele 

K otestování, že vaše aplikace byla publikována správně, přidejte testovací uživatelský účet. Ověřte, že tento účet už má oprávnění pro přístup k aplikaci uvnitř podnikové sítě.

1. Zpět v okně rychlý start vyberte **přiřadit uživatele pro testování**.

  ![Přiřadit uživatele pro testování](./media/application-proxy-publish-azure-portal/assign-user.png)

2. V modulu Uživatelé a skupiny okno Vyberte **přidat**.

  ![Přidat uživatele nebo skupinu](./media/application-proxy-publish-azure-portal/add-user.png)

3. V okně Přidat přiřazení, vyberte **uživatelů a skupin** zvolte účet, který chcete přidat. 
4. Vyberte **Přiřadit**.

## <a name="test-your-published-app"></a>Testování publikované aplikace

V prohlížeči přejděte na externí adresu URL, kterou jste nakonfigurovali během kroku publikovat. By měl zobrazit na obrazovce start a bude moct přihlásit se pomocí testovacího účtu, který nastavíte.

![Testování publikované aplikace](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Další postup
- [Stažení konektorů](application-proxy-enable.md) a [vytvořit skupiny konektorů](application-proxy-connector-groups.md) k publikování aplikací na samostatných sítí a umístění.

- [Nastavení jednotného přihlašování](application-proxy-configure-single-sign-on-password-vaulting.md) pro nově publikované aplikace
