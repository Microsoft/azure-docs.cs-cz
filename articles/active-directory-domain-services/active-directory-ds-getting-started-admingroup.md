---
title: 'Azure Active Directory Domain Services: Začínáme | Microsoft Docs'
description: Povolit Azure Active Directory Domain Services pomocí portálu Azure
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
ms.topic: article
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: d5b81a6d4bdda24208673e42757807aba60fea97
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263971"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Povolit Azure Active Directory Domain Services pomocí portálu Azure


## <a name="task-3-configure-administrative-group"></a>Úloha 3: Konfigurace skupiny pro správu
V této úloze konfigurace můžete vytvořit skupiny pro správu v adresáři služby Azure AD. Tuto speciální skupinu pro správu se nazývá *AAD řadič domény správci*. Členové této skupiny mají oprávnění správce na počítačích, které jsou připojené k doméně k spravované doméně. Na počítačích připojených k doméně je této skupiny přidat do skupiny administrators. Členové této skupiny navíc můžete použít vzdálené plochy se vzdáleně připojit k doméně počítače.

> [!NOTE]
> Nemáte oprávnění správce domény nebo správce podnikové sítě na spravované domény, který jste vytvořili pomocí Azure Active Directory Domain Services. Na spravovaných domén tato oprávnění jsou vyhrazené pomocí služby a nejsou k dispozici uživatelům v rámci klienta. Ale můžete vytvořit v této úloze konfigurace speciální skupinu pro správu provádět některé privilegované operace. Tyto operace zahrnují připojení počítače k doméně, které patří do skupiny správy na počítačích připojených k doméně a konfigurace zásad skupiny.
>

Průvodce automaticky vytvoří skupinu pro správu v adresáři služby Azure AD. Tato skupina se nazývá 'AAD řadič domény správci'. Pokud máte existující skupinu s tímto názvem v adresáři služby Azure AD, Průvodce vybírá této skupiny. Můžete nakonfigurovat skupinu členství pomocí **skupiny správců** stránce průvodce.

1. Chcete-li nakonfigurovat členství ve skupině, klikněte na tlačítko **AAD řadič domény správci**.

    ![Konfigurace členství ve skupinách](./media/getting-started/domain-services-blade-admingroup.png)

2. Klikněte **přidat členy** tlačítko Přidat uživatele z adresáře služby Azure AD do skupiny Administrators.

3. Až budete hotovi, klikněte na tlačítko **OK** přesunout na **Souhrn** stránce průvodce.


## <a name="deploy-your-managed-domain"></a>Nasadit spravované doméně

1. Na **Souhrn** stránky v průvodci zkontrolujte nastavení konfigurace pro spravované domény. Můžete přejít zpět do jakéhokoli kroku průvodce provést změny, v případě potřeby. Až budete hotovi, klikněte na tlačítko **OK** k vytvoření nové spravované domény.

    ![Souhrn](./media/getting-started/domain-services-blade-summary.png)

2. Zobrazí oznámení, že zobrazuje průběh nasazení služby Azure AD Domain Services. Kliknutím na oznámení najdete podrobný průběh nasazení.

    ![Oznámení – v průběhu nasazení](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="check-the-deployment-status-of-your-managed-domain"></a>Zkontrolujte stav nasazení vaší spravované domény
Proces zřizování vaší spravované domény může trvat až jednu hodinu.

1. Během nasazení, můžete vyhledat 'domain services, na **vyhledávání prostředků** vyhledávacího pole. Vyberte **Azure AD Domain Services** z výsledku hledání. **Azure AD Domain Services** okno uvádí spravované domény, který se připravuje.

    ![Najít spravované doméně, se zřídí](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klikněte na název spravované domény (například "contoso100.com") můžete zjistit podrobnosti o spravované domény.

    ![Doménových služeb – Stav zřizování](./media/getting-started/domain-services-provisioning-state.png)

3. **Přehled** kartě se zobrazují, že právě probíhá zřizování spravované domény. Spravované doméně nelze nakonfigurovat, dokud je plně zřízený. To může trvat až jednu hodinu vaší spravované domény kompletní zřízení.

    ![Doménových služeb – karta Přehled během Stav zřizování ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Pokud spravované doméně plně zřízený, **přehled** kartě se zobrazují stav domény jako **systémem**.

    ![Domain Services – Karta Přehled po úplném zřízení](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Azure AD Domain Services během procesu zřizování, vytvoří podnikové aplikace s názvem "Služby řadiče domény" a "AzureActiveDirectoryDomainControllerServices" v adresáři. Tyto podnikové aplikace jsou potřebné k služby vaší spravované domény. Je nutné, aby tyto nebudou odstraněny kdykoli.
    >

5. Na **vlastnosti** kartě uvidíte dvě IP adresy, které jsou k dispozici pro virtuální síť řadiče.

    ![Doménových služeb – karta Vlastnosti po plně zřízený.](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="need-help"></a>Potřebujete pomoct?
Může trvat hodinu nebo dvě pro oba řadiče domény vaší spravované domény, které se má zřídit. Pokud vaše nasazení se nezdařilo nebo je zachycen v '' čeká na zpracování déle než několik hodin, klidně si zatím [produktový tým požádejte o pomoc](active-directory-ds-contact-us.md).


## <a name="next-step"></a>Další krok
[Úloha 4: Aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
