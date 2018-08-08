---
title: Jak nakonfigurovat hybridní služby Azure Active Directory zařízení připojená k | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zařízení Azure Active Directory připojená k hybridní.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 9ffc84009adfca60e9ae6b188b65b15e874e7d9c
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622166"
---
# <a name="how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Jak řídit připojení k hybridní službě Azure AD. zařízení

Připojení k hybridní službě Azure AD je proces, na zařízení připojeném k doméně místní zařízení automaticky zaregistrovalo s Azure AD. Existují případy, kdy nechcete, aby všechna svá zařízení zaregistrovat automaticky. Toto je příklad hodnotu true, během počáteční uvedení, chcete-li ověřit, že vše funguje podle očekávání.

Tento článek obsahuje pokyny, jak můžete řídit hybridní připojení k Azure AD z vašich zařízení. 


## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s:

-  [Úvod do správy zařízení ve službě Azure Active Directory](../device-management-introduction.md)
 
-  [Plánování implementace hybridního připojení Azure Active Directory](hybrid-azuread-join-plan.md)

-  Konfigurace připojení k hybridní službě Azure Active Directory pro [spravované domény](hybrid-azuread-join-managed-domains.md) nebo [federované domény](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Ovládací prvek aktuální zařízení Windows

Pro zařízení s operačním systémem klasické pracovní plochy Windows, je podporovaná verze Windows 10 Anniversary Update (verze 1607) nebo novější. Jako osvědčený postup upgradujte na nejnovější verzi Windows 10.

Všechny systémy windows aktuální zařízení se automaticky zaregistrují do služby Azure AD na začátku zařízení nebo uživatele přihlásit. Toto chování pomocí objektu zásad skupiny (GPO) nebo System Center Configuration Manager můžete ovládat.

Pokud chcete řídit aktuální zařízení Windows, budete muset: 

1.  **Na všech zařízeních**: Zakázat automatické registrace zařízení.
2.  **Na vybraných zařízeních**: Povolit automatické registrace zařízení.

Pokud jste ověřili, že vše funguje podle očekávání, budete chtít povolit automatickou registraci zařízení pro všechna zařízení znovu.



## <a name="group-policy-object"></a>Objekt zásad skupiny 

Nasazením následujících zásad skupiny můžete řídit chování zařízení registraci svých zařízení: **zaregistrovat počítače připojené k doméně jako zařízení**

**Nastavení objektu zásad skupiny**:

1.  Otevřít **správce serveru**a pak přejděte na **nástroje \> Správa zásad skupiny**.

2.  Přejděte na uzel domény, který odpovídá doméně, ve které chcete zakázat nebo povolit automatickou registraci.

3.  Klikněte pravým tlačítkem na **objekty zásad skupiny**a pak vyberte **nový**.

4.  Zadejte název (například *připojení k hybridní službě Azure AD*) pro svůj objekt zásad skupiny. 

5.  Klikněte na **OK**.

6.  Klikněte pravým tlačítkem na nový objekt zásad skupiny a pak vyberte **upravit**.

7.  Přejděte na **konfigurace počítače \> zásady \> šablony pro správu \> součásti Windows \> registrace zařízení**. 

8.  Klikněte pravým tlačítkem na **zaregistrovat počítače připojené k doméně jako zařízení**a pak vyberte **upravit**.

    > [!NOTE] 
    > Tato šablona zásad skupiny byl přejmenován z dřívějších verzích konzoly pro správu zásad skupiny. Pokud používáte starší verzi konzoly, přejděte na **konfigurace počítače \> zásady \> šablony pro správu \> součásti Windows \> připojení pracovního místa \> Automaticky klientské počítače se připojení k síti na pracovišti**. 

9.  Vyberte jednu z následujících nastavení a potom klikněte na tlačítko **použít**:

    - **Zakázané** – Pokud chcete zabránit automatické registrace zařízení
    - **Povolené** – Pokud chcete povolit automatické registrace

10. Klikněte na **OK**.

Budete potřebovat odkázat objekt zásad skupiny do umístění podle vašeho výběru. Například nastavte tuto zásadu pro všechna aktuální zařízení připojených k doméně ve vaší organizaci, propojte objekt zásad skupiny s doménou. Provést řízené nasazení, nastavte tuto zásadu na připojeném k doméně Windows aktuální zařízení, která patří organizační jednotku nebo skupinu zabezpečení.

### <a name="configuration-manager-controlled-deployment"></a>Nasazení nástroje Configuration Manager řízený 

Pomocí následujícího klientského nastavení můžete řídit chování registrace zařízení aktuální zařízení: ** automaticky zaregistrovat nová zařízení s Windows 10 připojených k doméně se službou azure Active Directory **


**Chcete-li nastavit nastavení klienta**:

1.  Otevřít **nástroje Configuration Manager**a pak přejděte na **Cloud Services**.

2.  V části **nastavení zařízení**, vyberte jednu z následujících nastavení pro **automaticky zaregistrovat nová zařízení s Windows 10 připojených k doméně se službou azure Active Directory**:

    - **Ne** – Pokud chcete zabránit automatické registrace zařízení
    - **Ano** – Pokud chcete povolit automatické registrace


3.  Klikněte na **OK**.
    

Budete muset propojit toto klientské nastavení do umístění podle vašeho výběru. Například pokud chcete nakonfigurovat tato nastavení klienta pro všechna aktuální zařízení Windows ve vaší organizaci, propojte nastavení klienta k doméně. Provést řízené nasazení, můžete nakonfigurovat nastavení klienta pro připojené k doméně Windows aktuální zařízení, která patří k organizační jednotku nebo skupinu zabezpečení.

> [!Important]
> Při konfiguraci uvedené výš se postará o stávající zařízení s Windows 10 připojená k doméně, je v provozu nově domény připojení zařízení k stále snažit úplné hybridní služby Azure AD join kvůli možnému zpoždění aplikace skutečný zásad skupiny nebo Nastavení nástroje Configuration Manager nově zařízení s Windows 10 připojená k doméně. Abyste tomu předešli, doporučujeme vytvořit nové bitové kopie nástroje sysprep (jako příklad je použita pro metody zřizování) ze zařízení, které nikdy předtím připojená k hybridní Azure AD a že už obsahuje výše použijí nastavení zásad skupiny nebo klienta nástroje Configuration Manager nastavení použít. Nové bitové kopie musíte použít také ke zřizování nových počítačů, které doméně vaší organizace. 

## <a name="control-windows-down-level-devices"></a>Řízení zařízení Windows nižší úrovně

K registraci zařízení s Windows nižší úrovně, je potřeba stáhnout a nainstalovat balíček Instalační služby systému Windows (MSI) ze služby Stažení softwaru [Microsoft Workplace Join pro počítače s Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=53554) stránky.

Balíček můžete nasadit pomocí systém distribuce softwaru jako je System Center Configuration Manager. Balíček podporuje možnosti standardní tiché instalace s parametrem tichý. [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) Current Branch nabízí další výhody z dřívějších verzí, jako je schopnost sledování dokončení registrace.

Instalační program vytvoří naplánovanou úlohu v systému, na kterém běží v kontextu uživatele. Úloha se aktivuje, když uživatel přihlásí do Windows. Úloha tiše připojí zařízení s Azure AD s přihlašovacími údaji uživatele po ověření pomocí Azure AD.


Ke kontrole registrace zařízení, měli byste nasadit balíček Instalační služby systému Windows jenom pro vybrané skupiny zařízení Windows nižší úrovně. Pokud jste ověřili, že vše funguje podle očekávání, jste připraveni k nasazení balíčku na všech zařízeních s nižší úrovně.


## <a name="next-steps"></a>Další postup

* [Úvod do správy zařízení ve službě Azure Active Directory](../device-management-introduction.md)



