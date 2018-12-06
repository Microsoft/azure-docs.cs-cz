---
title: 'Azure Active Directory Domain Services: Začínáme | Dokumentace Microsoftu'
description: Povolit Azure Active Directory Domain Services pomocí webu Azure portal
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ergreenl
ms.openlocfilehash: ec51ba28c81e2aa2dbfe2a3dc55d9940c74ff673
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959848"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Povolit Azure Active Directory Domain Services pomocí webu Azure portal

## <a name="task-3-configure-administrative-group"></a>Úloha 3: Konfigurace skupiny pro správu

V této úloze konfigurace vytvoření skupiny pro správu v adresáři služby Azure AD. Tato zvláštní skupiny pro správu se nazývá *správci AAD DC*. Členové této skupiny jsou udělena oprávnění správce na počítačích, které jsou připojené ke spravované doméně. Na počítačích připojených k doméně se této skupiny přidá do skupiny administrators. Členové této skupiny navíc můžete použít ke vzdálené ploše vzdálené připojení k doméně počítače.

> [!NOTE]
> Nemáte oprávnění správce domény nebo správce podnikové sítě ve spravované doméně, kterou jste vytvořili pomocí Azure Active Directory Domain Services. Na spravovaných domén tato oprávnění jsou vyhrazené služby a nejsou k dispozici uživatelům v tenantovi. Ale můžete použít speciální vytvořené v tomto úkolu konfigurace skupiny pro správu provádět některé privilegované operace. Tyto operace patří připojení počítače k doméně, které patří do skupiny správy na počítačích připojených k doméně a konfigurace zásad skupiny.
>

Průvodce automaticky vytvoří příslušné skupiny pro správu v adresáři služby Azure AD. Tato skupina se nazývá "Správci AAD DC". Pokud máte s tímto názvem existující skupiny v adresáři služby Azure AD, průvodce vybere tuto skupinu. Můžete nakonfigurovat pomocí členství ve skupině **skupiny správců** stránky průvodce.

1. Ke konfiguraci členství ve skupině, klikněte na tlačítko **správci AAD DC**.

    ![Konfigurace členství ve skupinách](./media/getting-started/domain-services-blade-admingroup.png)

2. Klikněte na tlačítko **přidat členy** tlačítko Přidat uživatele z adresáře služby Azure AD do skupiny správců.

3. Jakmile budete hotovi, klikněte na tlačítko **OK** přesunout do **Souhrn** stránky průvodce.

## <a name="configure-synchronization"></a>Konfigurace synchronizace

Azure AD Domain Services umožňuje buď úplná synchronizace všech uživatelů a skupin, které jsou k dispozici ve službě Azure AD, nebo můžete vybrat s vymezeným oborem synchronizace pro synchronizaci jenom na konkrétní skupiny. Pokud vyberete možnost Úplná synchronizace, bude **není** zvolit s vymezeným oborem synchronizace později. Další informace o synchronizaci s vymezeným oborem, najdete [obor článku synchronizace Azure AD Domain Services](active-directory-ds-scoped-synchronization.md).

### <a name="full-synchronization"></a>Úplná synchronizace

1. Pro úplnou synchronizaci, stačí kliknout na "OK" v dolní části obrazovky jako plně je již vybrána.
    ![Úplná synchronizace](./media/active-directory-domain-services-admin-guide/create-sync-all.PNG)

### <a name="scoped-synchronization"></a>Synchronizace s vymezeným oborem

1. Přepínací tlačítko synchronizace "Obor" a na stránce Vybrat skupiny se zobrazí. Z tohoto uvidíte, jaké skupiny jsou již vybrány synchronizovat do spravované domény.
    ![Synchronizace s vymezeným oborem](media/active-directory-domain-services-admin-guide/create-sync-scoped.PNG)
2. Klikněte na tlačítko **vybrat skupiny** v horním navigačním panelu. Z tohoto místa ovládacího prvku pro výběr skupiny objeví na straně. Použijte a vyberte všechny další skupiny pro synchronizaci Azure AD Domain Services. Až budete hotovi, klikněte na tlačítko **vyberte** zavřete Nástroj pro výběr skupiny a přidejte tyto skupiny do vybraného seznamu.
    ![Vyberte skupiny s vymezeným oborem synchronizace](media/active-directory-domain-services-admin-guide/create-sync-scoped-groupselect.PNG)
3. Klikněte na tlačítko **OK** přejděte na stránku Souhrn v.

## <a name="deploy-your-managed-domain"></a>Nasazení vaší spravované domény

1. Na **Souhrn** stránku průvodce, projděte si nastavení konfigurace pro spravovanou doménu. Můžete přejít zpět na libovolný krok průvodce a proveďte změny v případě potřeby. Jakmile budete hotovi, klikněte na tlačítko **OK** k vytvoření nové spravované domény.

    ![Souhrn](./media/getting-started/domain-services-blade-summary.png)

2. Se zobrazí oznámení, který znázorňuje průběh nasazení služby Azure AD Domain Services. Klikněte na oznámení zobrazíte podrobné průběh nasazení.

    ![Oznámení – probíhá nasazení](./media/getting-started/domain-services-blade-deployment-in-progress.png)

## <a name="check-the-deployment-status-of-your-managed-domain"></a>Zkontrolujte stav nasazení vaší spravované domény

Proces zřizování vaši spravovanou doménu může trvat až hodinu.

1. Zatímco probíhá nasazení, můžete vyhledat "domain services" v **vyhledat prostředky** vyhledávacího pole. Vyberte **Azure AD Domain Services** z výsledku hledání. **Azure AD Domain Services** okno uvádí spravované domény, který se zřizuje.

    ![Najít zřizuje spravované domény](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klikněte na název spravované domény (například "contoso100.com") zobrazíte další podrobnosti o spravované domény.

    ![Domain Services – Stav zřizování](./media/getting-started/domain-services-provisioning-state.png)

3. **Přehled** kartě se zobrazí, že spravovaná doména se v tuto chvíli zřizuje. Nelze nakonfigurovat spravované domény, dokud je plně zřízený. Může trvat až hodinu jejich kompletní zřízení spravované domény.

    ![Domain Services – karta Přehled během Stav zřizování ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Když je spravovaná doména úplně zřízená, **přehled** karta zobrazuje stav domény jako **systémem**.

    ![Domain Services – Karta Přehled po úplném zřízení](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Během procesu zřizování Azure AD Domain Services vytvoří podnikové aplikace s názvem "Služby řadiče domény" a "AzureActiveDirectoryDomainControllerServices" ve svém adresáři. Tyto podnikové aplikace jsou potřeba pro služby spravované domény. Je nutné tyto nejsou smazat kdykoli.
    >

5. Na **vlastnosti** kartu, se zobrazí dvě IP adresy, na kterých je služba domain řadiče jsou k dispozici pro virtuální síť.

    ![Domain Services – karta Vlastnosti po úplném zřízení](./media/getting-started/domain-services-provisioned-properties.png)

## <a name="need-help"></a>Potřebujete pomoct?

Může trvat hodinu nebo dvě pro oba řadiče domény vaší spravované domény, které se mají zřídit. Pokud vaše nasazení se nezdařilo nebo je zablokované ve stavu 'Čekající na vyřízení' pro více než několik hodin, bez obav [kontaktováním produktového týmu pomoc](active-directory-ds-contact-us.md).

## <a name="next-step"></a>Další krok

[Úloha 4: Aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
