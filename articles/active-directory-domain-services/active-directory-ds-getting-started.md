---
title: 'Azure Active Directory Domain Services: Začínáme | Dokumentace Microsoftu'
description: Povolit Azure Active Directory Domain Services pomocí webu Azure portal
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 4c533921b0c88a4f61cd96896b72306adb9a23cf
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175702"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Povolit Azure Active Directory Domain Services pomocí webu Azure portal
Tento článek ukazuje, jak povolit Azure Active Directory Domain Services (Azure AD DS) pomocí webu Azure portal.


## <a name="before-you-begin"></a>Před zahájením
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

    ![Search for domain services](./media/getting-started/search-domain-services.png)

4. Kliknutím vyberte **Azure AD Domain Services** ze seznamu návrhy hledání. Na **Azure AD Domain Services** stránky, klikněte na tlačítko **vytvořit** tlačítko.

    ![Domain services view](./media/getting-started/domain-services-blade.png)

5. **Povolit Azure AD Domain Services** se spustí průvodce.


## <a name="task-1-configure-basic-settings"></a>Úloha 1: Konfigurace základního nastavení
V **Základy** stránku průvodce, zadejte název domény DNS pro spravovanou doménu. Můžete také zvolit skupinu prostředků a umístění Azure, ke kterému by měly být nasazeny spravované domény.

![Základní informace o konfiguraci](./media/getting-started/domain-services-blade-basics.png)

1. Zvolte **název domény DNS** vaší spravované domény.

   > [!NOTE]
   > **Pokyny pro výběr názvu domény DNS**
   > * **Název předdefinované domény:** Ve výchozím nastavení, průvodce určuje název výchozí/vestavěné domény adresáře (s **. onmicrosoft.com** přípona) za vás. Pokud se rozhodnete povolit přístup ke spravované doméně secure LDAP přes internet, očekávat, že problémy s vytvořením veřejný záznam DNS nebo získání certifikátem protokolu secure LDAP od veřejné certifikační Autority pro tento název domény. Microsoft je vlastníkem *. onmicrosoft.com* domény a certifikačních autorit nevydá certifikáty ručící pro tuto doménu.
   * **Vlastní názvy domén:** Můžete také zadat název vlastní domény. V tomto příkladu je použit vlastní název domény *contoso100.com*.
   * **Přípony směrovatelných v jiných domén:** Obecně doporučujeme přípony názvu nesměrovatelných domény. Například je lepší vyhýbat vytváření domény s názvem domény DNS "contoso.local". Přípona DNS ".local" není směrovatelný a může způsobit problémy s překladem DNS.
   * **Omezení předpona domény:** Předpona zadaného názvu domény (například contoso100 v případě názvu domény *contoso100.com*) musí obsahovat nejvýše 15 znaků. Nelze vytvořit spravovanou doménu s předponou delší než 15 znaků.
   * **Název konflikty v síti:** Ujistěte se, že vámi zvolený název domény DNS pro spravovanou doménu ještě ve virtuální síti neexistuje. Zejména zkontrolujte, zda:
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
