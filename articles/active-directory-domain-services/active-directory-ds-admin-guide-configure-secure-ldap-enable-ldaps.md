---
title: Konfigurace zabezpečeného LDAP (LDAPS) ve službě Azure AD Domain Services | Microsoft Docs
description: Konfigurace zabezpečení protokolu LDAP (LDAPS) pro spravované doméně služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: maheshu
ms.openlocfilehash: 4263034408de059880b91e8106f6832ccacc6085
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300964"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurace zabezpečeného LDAP (LDAPS) pro spravované doméně služby Azure AD Domain Services

## <a name="before-you-begin"></a>Než začnete
Ujistěte se, když jste dokončili [úloha 2 - zabezpečený LDAP certifikát, který chcete exportovat. Soubor PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Úloha 3 – povolení zabezpečeného LDAP pro spravované doméně pomocí portálu Azure
Pokud chcete povolit zabezpečený LDAP, proveďte následující kroky konfigurace:

1. Přejděte na  **[portál Azure](https://portal.azure.com)**.

2. Vyhledejte 'domain services' v **vyhledávání prostředků** vyhledávacího pole. Vyberte **Azure AD Domain Services** z výsledku hledání. **Azure AD Domain Services** stránka obsahuje seznam vaší spravované domény.

    ![Najít spravované doméně, se zřídí](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klikněte na název spravované domény (například "contoso100.com") můžete zjistit podrobnosti o doméně.

    ![Doménových služeb – Stav zřizování](./media/getting-started/domain-services-provisioning-state.png)

3. Klikněte na tlačítko **zabezpečení LDAP** v navigačním podokně.

    ![Doménových služeb – stránka zabezpečený LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Zabezpečený LDAP přístup k vaší spravované domény je ve výchozím nastavení zakázaná. Přepnutí **zabezpečený LDAP** k **povolit**.

    ![Povolit zabezpečený LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Zabezpečený LDAP přístup k vaší spravované domény přes internet je ve výchozím nastavení zakázaná. Přepnutí **povolit zabezpečený LDAP přístup přes internet** k **povolit**, v případě potřeby. 

    > [!WARNING]
    > Když povolíte zabezpečený LDAP přístup přes internet, vaše doména je náchylný na útoky hrubou silou hesla přes internet. Proto doporučujeme nastavení skupiny NSG k zablokovat přístup do požadovaných zdrojových rozsahů IP adres. Postupujte podle pokynů k [uzamčení LDAPS přístup k vaší spravované domény přes internet](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Klikněte na ikonu následující složky **. Soubor PFX s certifikátem zabezpečení LDAP**. Zadejte cestu k souboru PFX pomocí certifikátu pro zabezpečený přístup protokolu LDAP k spravované doméně.

7. Zadejte **heslo pro dešifrování. Soubor PFX**. Zadejte stejné heslo, které jste použili při exportu certifikátu do souboru PFX.

8. Až budete hotovi, klikněte na **Uložit** tlačítko.

9. Uvidíte, že oznámení, že informuje o tom, že zabezpečený LDAP je konfigurován pro spravovanou doménu. Až do dokončení této operace, nelze změnit další nastavení pro doménu.

    ![Konfigurace zabezpečeného LDAP pro spravované doméně](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Chcete-li povolit zabezpečený LDAP vaší spravované domény trvá asi 10 až 15 minut. Pokud k zadanému zabezpečený LDAP certifikátu neodpovídá požadované kritéria, zabezpečený LDAP není povoleno pro svůj adresář a zobrazit informace o selhání. Například je nesprávný název domény, certifikátu již vypršela nebo brzo vyprší. V takovém případě zkuste to znovu s platným certifikátem.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Úloha 4: Konfigurace DNS pro přístup k spravované doméně z Internetu
> [!NOTE]
> **Nepovinná úloha** – Pokud neplánujete přístup ke spravované doméně pomocí LDAPS přes internet, přeskočte tento úkol konfigurace.
>
>

Než začnete tuto úlohu, zkontrolujte jste dokončili podle kroků uvedených v [úloha 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Jakmile povolíte zabezpečený LDAP přístup přes internet vaší spravované domény, je potřeba aktualizovat DNS, aby klientské počítače najít této spravované domény. Na konci úloha 3 externí IP adresa se zobrazí na **vlastnosti** kartě v **externí IP adresu pro LDAPS přístup**.

Nakonfigurujte externího poskytovatele DNS, aby název DNS spravované doméně (například "ldaps.contoso100.com') odkazuje na tato externí IP adresu. Můžete například vytvořte následující položku DNS:

    ldaps.contoso100.com  -> 52.165.38.113

Je to – nyní jste připraveni k připojení k spravované doméně pomocí zabezpečený LDAP přes internet.

> [!WARNING]
> Mějte na paměti, že klientské počítače musí důvěřovat vystavitele certifikátu LDAPS moct úspěšně připojit k spravované doméně pomocí LDAPS. Pokud používáte veřejně důvěryhodné certifikační autority, není potřeba dělat nic, protože klientské počítače důvěřovat tyto vystavitelů certifikátů. Pokud používáte certifikát podepsaný svým držitelem, nainstalujte část veřejný certifikát podepsaný svým držitelem do úložiště důvěryhodných certifikátů v klientském počítači.
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Úloha 5: uzamčení zabezpečený LDAP přístup k vaší spravované domény přes internet
> [!NOTE]
> Pokud jste nepovolili LDAPS přístup k spravované doméně přes internet, přeskočte tento úkol konfigurace.
>
>

Než začnete tuto úlohu, zkontrolujte jste dokončili podle kroků uvedených v [úloha 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Vystavení vaší spravované domény pro LDAPS přístup přes internet představuje bezpečnostní riziko. Spravované doméně je dosažitelný z Internetu na port používaný pro zabezpečený LDAP (to znamená, port 636). Proto můžete omezit přístup k spravované doméně na konkrétní známé IP adresy. Pro lepší zabezpečení vytvořte skupinu zabezpečení sítě (NSG) a přidružte ji k podsíti, kde jste povolili službu Azure AD Domain Services.

Následující tabulka znázorňuje ukázku NSG můžete nakonfigurovat, zamknout zabezpečený LDAP přístup přes internet. NSG obsahuje sadu pravidel, která povolí příchozí zabezpečený LDAP přístup přes port TCP 636 pouze ze zadané sady IP adres. Výchozí pravidlo, DenyAll, platí pro všechny ostatní příchozí přenosy z Internetu. Pravidla NSG pro povolení LDAPS přístupu přes internet ze zadaných IP adres má vyšší prioritu než skupina NSG DenyAll pravidlo.

![Ukázka skupiny NSG k zabezpečení přístupu k LDAPS přes internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Další informace** - [skupin zabezpečení sítě](../virtual-network/security-overview.md).

<br>


## <a name="troubleshooting"></a>Řešení potíží
Pokud máte potíže s připojením k spravované doméně pomocí zabezpečený LDAP, proveďte následující kroky řešení potíží:
* Ujistěte se, že řetězu vystavitele certifikátu zabezpečeného LDAP je důvěryhodná v klientovi. Můžete se rozhodnout pro přidání kořenové certifikační autority do úložiště důvěryhodných kořenových certifikátů na straně klienta k navázání vztahu důvěryhodnosti.
* Ověřte, že klienta LDAP (například ldp.exe) připojí k zabezpečení koncového bodu protokolu LDAP pomocí názvu DNS, ne IP adresy.
* Ověřte název DNS, který klient LDAP připojí k překládá se na veřejnou IP adresu pro zabezpečený LDAP na spravované domény.
* Ověřte, že zabezpečený LDAP certifikát pro vaší spravované domény má název DNS v subjektu nebo alternativní názvy subjektu atribut.
* Pokud se připojujete přes zabezpečený LDAP přes internet, ověřte, že skupina NSG nastavení pro virtuální síť povolit provoz na port 636 z Internetu.

Pokud máte potíže s připojením k spravované doméně pomocí zabezpečený LDAP [obraťte se na tým produktu](active-directory-ds-contact-us.md) nápovědu. Zahrnout tyto informace pomohou lépe diagnostikovat potíže:
* Snímek obrazovky ldp.exe provedením připojení a selhání.
* Vaše ID klienta Azure AD a název domény DNS vaší spravované domény.
* Přesné uživatelské jméno, které se pokoušíte vytvořit vazbu jako.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Správa zásad skupiny na spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md)
* [Vytvořit skupinu zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)
