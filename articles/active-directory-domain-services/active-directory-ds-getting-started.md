---
title: 'Azure Active Directory Domain Services: Začínáme | Dokumentace Microsoftu'
description: Povolit Azure Active Directory Domain Services pomocí webu Azure portal
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: b6651c038a2b3abd15b8b0587e6a0e95832401b1
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502307"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Povolit Azure Active Directory Domain Services pomocí webu Azure portal
Tento článek ukazuje, jak povolit Azure Active Directory Domain Services (Azure AD DS) pomocí webu Azure portal.


## <a name="before-you-begin"></a>Než začnete
K dokončení úkolů uvedených v tomto článku, budete potřebovat:

* Platný **předplatného Azure**.
* **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
* **Předplatné Azure musí být přidružená ke službě Azure AD directory**.
* Potřebujete **globálního správce** oprávnění v adresáři služby Azure AD pro povolení služby Azure AD Domain Services.


## <a name="enable-azure-ad-domain-services"></a>Povolení služby Azure AD Domain Services

Ke spuštění **povolit Azure AD Domain Services** průvodce, proveďte následující kroky:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V levém podokně klikněte na **Vytvořit prostředek**.
3. V **nový** zadejte **Domain Services** do panelu hledání.

    ![Vyhledávání pro službu domain services](./media/getting-started/search-domain-services.png)

4. Kliknutím vyberte **Azure AD Domain Services** ze seznamu návrhy hledání. Na **Azure AD Domain Services** stránky, klikněte na tlačítko **vytvořit** tlačítko.

    ![Zobrazení služeb domény](./media/getting-started/domain-services-blade.png)

5. **Povolit Azure AD Domain Services** se spustí průvodce.


## <a name="task-1-configure-basic-settings"></a>Úloha 1: Konfigurace základního nastavení
V **Základy** stránku průvodce, zadejte název domény DNS pro spravovanou doménu. Můžete také zvolit skupinu prostředků a umístění Azure, ke kterému by měly být nasazeny spravované domény.

![Základní informace o konfiguraci](./media/getting-started/domain-services-blade-basics.png)

1. Zvolte **název domény DNS** vaší spravované domény.

   > [!NOTE]
   > **Pokyny pro výběr názvu domény DNS**
   > * **Název předdefinované domény:** ve výchozím nastavení, průvodce určuje název domény výchozí/vestavěné adresáře (s **. onmicrosoft.com** přípona) za vás. Pokud se rozhodnete povolit přístup ke spravované doméně secure LDAP přes internet, očekávat, že problémy s vytvořením veřejný záznam DNS nebo získání certifikátem protokolu secure LDAP od veřejné certifikační Autority pro tento název domény. Microsoft je vlastníkem *. onmicrosoft.com* domény a certifikačních autorit nevydá certifikáty ručící pro tuto doménu.
   * **Vlastní názvy domén:** také můžete zadat vlastní název domény. V tomto příkladu je použit vlastní název domény *contoso100.com*.
   * **Přípony směrovatelných v jiných domén:** obecně doporučujeme přípony názvu nesměrovatelných domény. Například je lepší vyhýbat vytváření domény s názvem domény DNS "contoso.local". Přípona DNS ".local" není směrovatelný a může způsobit problémy s překladem DNS.
   * **Omezení domény předpona:** předpona zadaného názvu domény (například *contoso100* v *contoso100.com* název domény) musí obsahovat nejvýše 15 znaků. Nelze vytvořit spravovanou doménu s předponou delší než 15 znaků.
   * **Název je v konfliktu sítě:** Ujistěte se, že název domény DNS, který jste zvolili pro spravovanou doménu ve virtuální síti už neexistuje. Zejména zkontrolujte, zda:
       * Už máte doménu služby Active Directory se stejným názvem domény DNS ve virtuální síti.
       * Virtuální síť, ve kterém budete chtít povolit spravované domény má připojení VPN s vaší místní sítí. V tomto scénáři Ujistěte se, že nemáte doménu se stejným názvem domény DNS ve vaší místní síti.
       * Ve virtuální síti existuje cloudová služba s tímto názvem.
    >

2. Vyberte Azure **předplatné** ve kterém chcete vytvořit spravovanou doménu.

3. Vyberte **skupiny prostředků** do spravované domény by měly patřit. Zvolte buď **vytvořit nový** nebo **použít existující** možnosti a vyberte skupinu prostředků.

4. Zvolte Azure **umístění** ve které by měl vytvářet spravované domény. Na **sítě** stránky průvodce se zobrazí pouze virtuální sítě patřící do umístění, které jste vybrali.

5. Klikněte na tlačítko **OK** přesunout do **sítě** stránky průvodce.


## <a name="next-step"></a>Další krok
[Úloha 2: Konfigurace nastavení sítě](active-directory-ds-getting-started-network.md)
