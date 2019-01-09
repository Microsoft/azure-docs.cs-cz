---
title: Ovládací prvek připojení k hybridní službě Azure AD vašeho zařízení | Dokumentace Microsoftu
description: Zjistěte, jak řídit Azure AD join hybridní zařízení ve službě Azure Active Directory.
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
ms.openlocfilehash: a2ae1d3f4166bfaa035902aaa5dc101636a98646
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117533"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>Řízení připojení vašich zařízení k hybridní službě Azure AD

Připojení k hybridní službě Azure Active Directory (Azure AD) je proces, na zařízení připojeném k doméně místní zařízení automaticky zaregistrovalo s Azure AD. Existují případy, kdy nechcete, aby všechna vaše zařízení se dá registrovat automaticky. To platí, například během počáteční uvedení, chcete-li ověřit, že vše funguje podle očekávání.

Tento článek obsahuje pokyny na tom, jak ovládací prvek hybridní připojení k Azure AD z vašich zařízení. 


## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že už znáte:

-  [Úvod do správy zařízení v Azure Active Directory](../device-management-introduction.md)
 
-  [Naplánování vaší implementace připojení k hybridní službě Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Konfigurace připojení k hybridní službě Azure Active Directory službě pro spravované domény](hybrid-azuread-join-managed-domains.md) nebo [konfigurovat připojení k hybridní službě Azure Active Directory službě u federovaných domén](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Ovládací prvek aktuální zařízení Windows

Pro zařízení s operačním systémem klasické pracovní plochy Windows, je podporovaná verze Windows 10 Anniversary Update (verze 1607) nebo novější. Jako osvědčený postup upgradujte na nejnovější verzi Windows 10.

Přihlášení všechna aktuální zařízení se automaticky zaregistrují do služby Azure AD na začátku zařízení nebo uživatele Windows. Toto chování můžete řídit pomocí objektů zásad skupiny (GPO) nebo System Center Configuration Manager.

Pokud chcete řídit aktuální zařízení Windows, budete muset: 


1.  **Na všech zařízeních**: Zakážete automatické registrace zařízení.
2.  **Na vybraných zařízeních**: Povolte automatickou registraci zařízení.

Po ověření, že vše funguje podle očekávání, jste připraveni k povolení automatické registrace zařízení pro všechna zařízení znovu.



### <a name="group-policy-object"></a>Objekt zásad skupiny 

Nasazením následujících zásad skupiny můžete řídit chování zařízení registraci svých zařízení: **Zaregistrujte se jako zařízení připojených k doméně počítače**.

Nastavení objektu zásad skupiny:

1.  Otevřít **správce serveru**a pak přejděte na **nástroje** > **Správa zásad skupiny**.

2.  Přejděte na uzel domény, který odpovídá doméně, ve které chcete zakázat nebo povolit automatickou registraci.

3.  Klikněte pravým tlačítkem na **objekty zásad skupiny**a pak vyberte **nový**.

4.  Zadejte název (například **připojení k hybridní službě Azure AD**) pro svůj objekt zásad skupiny. 

5.  Vyberte **OK**.

6.  Klikněte pravým tlačítkem na nový objekt zásad skupiny a pak vyberte **upravit**.

7.  Přejděte na **konfigurace počítače** > **zásady** > **šablony pro správu** > **Windows Součásti** > **registrace zařízení**. 

8.  Klikněte pravým tlačítkem na **zaregistrovat počítače připojené k doméně jako zařízení**a pak vyberte **upravit**.

    > [!NOTE] 
    > Tato šablona zásad skupiny byl přejmenován z dřívějších verzích konzoly pro správu zásad skupiny. Pokud používáte starší verzi konzoly, přejděte na **konfigurace počítače** > **zásady** > **šablony pro správu**  >  **Součásti Windows** > **Workplace Join** > **automaticky klientské počítače se připojení k síti na pracovišti**. 

9.  Vyberte jednu z následujících nastavení a pak vyberte **použít**:

    - **Zakázané**: Aby se zabránilo automatické registrace zařízení.
    - **Povolené**: Povolit automatickou registraci zařízení.

10. Vyberte **OK**.

Budete potřebovat odkázat objekt zásad skupiny do umístění podle vašeho výběru. Například nastavte tuto zásadu pro všechna aktuální zařízení připojených k doméně ve vaší organizaci, propojte objekt zásad skupiny s doménou. Provést řízené nasazení, nastavte tuto zásadu na připojeném k doméně Windows aktuální zařízení, která patří organizační jednotku nebo skupinu zabezpečení.

### <a name="configuration-manager-controlled-deployment"></a>Nasazení nástroje Configuration Manager řízený 

Pomocí následujícího klientského nastavení můžete řídit chování registrace zařízení aktuální zařízení: **Automaticky zaregistrovat nová zařízení s Windows 10 připojených k doméně se službou Azure Active Directory**.

Postup konfigurace nastavení klienta:

1.  Otevřít **nástroje Configuration Manager**a pak přejděte na **Cloud Services**.

2.  V části **nastavení zařízení**, vyberte jednu z následujících nastavení pro **automaticky zaregistrovat nová zařízení s Windows 10 připojených k doméně se službou Azure Active Directory**:

    - **Ne**: Aby se zabránilo automatické registrace zařízení.
    - **Ano**: Povolit automatickou registraci zařízení.


3.  Vyberte **OK**.
    

Budete muset propojit toto klientské nastavení do umístění podle vašeho výběru. Například pokud chcete nakonfigurovat tato nastavení klienta pro všechna aktuální zařízení Windows ve vaší organizaci, propojte nastavení klienta k doméně. Provést řízené nasazení, můžete nakonfigurovat nastavení klienta pro připojené k doméně Windows aktuální zařízení, která patří k organizační jednotku nebo skupinu zabezpečení.

> [!Important]
> Ačkoli předchozí konfigurace se postará o stávající zařízení s Windows 10 připojených k doméně, zařízení, které jsou nově připojení k doméně se přesto může pokusit dokončete připojení k hybridní službě Azure AD z důvodu možných zpoždění při použití zásad skupiny nebo Nastavení nástroje Configuration Manager na zařízeních. 
>
> Abyste tomu předešli, doporučujeme vytvořit nové bitové kopie nástroje Sysprep (jako příklad je použita pro metody zřizování). Vytvořte ho ze zařízení, které nikdy předtím připojená k hybridní Azure AD a že již má zásady skupiny při nastavování nebo použít nastavení klienta nástroje Configuration Manager. Nové bitové kopie musíte použít také ke zřizování nových počítačů, které doméně vaší organizace. 

## <a name="control-windows-down-level-devices"></a>Řízení zařízení s Windows nižší úrovně

K registraci zařízení s Windows nižší úrovně, je potřeba stáhnout a nainstalovat balíček Instalační služby systému Windows (MSI) ze služby Stažení softwaru [Microsoft Workplace Join pro počítače s Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) stránky.

Balíček můžete nasadit pomocí systém distribuce softwaru, jako je [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Balíček podporuje možnosti standardní tichou instalaci s parametrem tichý. Aktuální větev nástroje Configuration Manager nabízí výhody starší verze, jako je schopnost sledování dokončení registrace.

Instalační program vytvoří naplánovanou úlohu v systému, na kterém běží v kontextu uživatele. Úloha se aktivuje, když uživatel přihlásí do Windows. Úloha tiše připojí zařízení s Azure AD s přihlašovacími údaji uživatele po ověření pomocí Azure AD.

Ke kontrole registrace zařízení, měli byste nasadit balíček Instalační služby systému Windows jenom pro vybrané skupiny zařízení Windows nižší úrovně. Pokud jste ověřili, že vše funguje podle očekávání, budete připraveni pro distribuci balíčku na všech zařízeních s nižší úrovně.


## <a name="next-steps"></a>Další postup

* [Úvod do správy zařízení v Azure Active Directory](../device-management-introduction.md)



