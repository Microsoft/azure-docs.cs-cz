---
title: "Povolit Microsoft Windows Hello pro firmy ve vaší organizaci | Microsoft Docs"
description: "Pokyny k nasazení povolit Microsoft Passport ve vaší organizaci."
services: active-directory
documentationcenter: 
keywords: "Konfigurace Microsoft Passport, Microsoft Windows Hello pro firmy nasazení"
author: MarkusVi
manager: mtillman
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 0aa16e3466b36b6d1d83308cf37623aa15d61fcb
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2018
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Povolit Microsoft Windows Hello pro firmy ve vaší organizaci
Po [připojení zařízení s Windows 10 připojených k doméně se službou Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), proveďte postup pro povolení Microsoft Windows Hello pro firmy v organizaci:

1. Nasazení nástroje System Center Configuration Manager  
2. Konfigurace nastavení zásad
3. Konfigurace profilu certifikátu  

## <a name="deploy-system-center-configuration-manager"></a>Nasazení nástroje System Center Configuration Manager
Pokud chcete nasadit uživatelské certifikáty založené na Windows Hello pro firmy klíče, budete potřebovat následující:

* **System Center Configuration Manager aktuální větev** – je potřeba nainstalovat verzi 1606 nebo vyšší. Další informace najdete v tématu [dokumentace pro System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) a [Blog týmu nástroje System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **Infrastruktury veřejných klíčů (PKI)** – Pokud chcete povolit Microsoft Windows Hello pro firmy pomocí uživatelských certifikátů, musíte mít infrastrukturu veřejných KLÍČŮ na místě. Pokud nemáte nebo nechcete používat pro certifikáty uživatelů, můžete nasadit nový řadič domény s Windows Server 2016 sestavení 10551 (nebo lepší) nainstalována. Postup [instalace repliky řadiče domény v existující doméně](https://technet.microsoft.com/library/jj574134.aspx) nebo [instalaci nové doménové struktury služby Active Directory, pokud vytváříte nové prostředí](https://technet.microsoft.com/library/jj574166). (Soubory ISO jsou k dispozici ke stažení na [Signiant média Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## <a name="configure-policy-settings"></a>Konfigurace nastavení zásad
Pokud chcete nakonfigurovat Microsoft Windows Hello pro firmy nastavení zásad, máte dvě možnosti:

* Zásady skupiny ve službě Active Directory 
* System Center Configuration Manager 

Pomocí zásad skupiny ve službě Active Directory je doporučená metoda konfigurace Microsoft Windows Hello pro firmy nastavení zásad. 

Pomocí nástroje System Center Configuration Manager je preferovanou metodu, když ho také použít k nasazení certifikátů. Tento scénář:

* Zajišťuje kompatibilitu s novější scénáře nasazení
* Vyžaduje na straně klienta Windows 10 verze 1607 nebo lepší.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Konfigurace Microsoft Windows Hello pro firmy prostřednictvím zásad skupiny ve službě Active Directory
**Kroky**:

1. Otevřete správce serveru a přejděte do **nástroje** > **Správa zásad skupiny**.
2. Správa zásad skupiny přejděte k uzlu domény, který odpovídá k doméně, ve kterém chcete povolit připojení ke službě Azure AD.
3. Klikněte pravým tlačítkem na **objekty zásad skupiny**a vyberte **nový**. Pojmenujte objektu zásad skupiny, například povolení Windows Hello pro firmy. Klikněte na **OK**.
4. Klikněte pravým tlačítkem na nový objekt zásad skupiny a potom vyberte **upravit**.
5. Přejděte na **konfigurace počítače** > **zásady** > **šablony pro správu** > **Windows Součásti** > **Windows Hello pro firmy**.
6. Klikněte pravým tlačítkem na **povolit Windows Hello pro firmy**a potom vyberte **upravit**.
7. Vyberte **povoleno** a pak klikněte na tlačítko **použít**. Klikněte na **OK**.
8. Teď můžete objekt zásad skupiny se propojit k umístění podle vaší volby. Chcete-li tato zásada u všech zařízení připojených k doméně Windows 10 ve vaší organizaci, propojte zásad skupiny do domény. Příklad:
   * Konkrétní organizační jednotku (OU) ve službě Active Directory, kde budou umístěné počítače s Windows 10 připojených k doméně
   * Skupinu zabezpečení, která obsahuje připojený k doméně počítače Windows 10, které se budou automaticky registrovat s Azure AD

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Konfigurace Windows Hello pro firmy pomocí System Center Configuration Manager
**Kroky**:

1. Otevřete **System Center Configuration Manager**a potom přejděte na **prostředky a kompatibilita > Nastavení dodržování předpisů > přístup k prostředkům společnosti > Windows Hello pro firmy profily**.
   
    ![Konfigurace Windows Hello pro firmy](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. Na panelu nástrojů v horní části klikněte na tlačítko **vytvořit Windows Hello pro firmy profil**.
   
    ![Konfigurace Windows Hello pro firmy](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. Na **Obecné** dialogové okno, proveďte následující kroky:
   
    ![Konfigurace Windows Hello pro firmy](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. V **název** textovému poli, zadejte název pro svůj profil, například **Můj profil WHfB**.
   
    b. Klikněte na **Další**.
4. Na **podporované platformy** dialogovém okně, vyberte platformy, které se budou zřizovat s Tento profil Windows Hello pro firmy a pak klikněte na tlačítko **Další**.
   
    ![Konfigurace Windows Hello pro firmy](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. Na **nastavení** dialogové okno, proveďte následující kroky:
   
    ![Konfigurace Windows Hello pro firmy](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Jako **konfigurace Windows Hello pro firmy**, vyberte **povoleno**.
   
    b. Jako **používat (Trusted Platform Module)**, vyberte **požadované**. 
   
    c. Jako **metodu ověřování**, vyberte **založené na certifikátech**.
   
    d. Klikněte na **Další**.
6. Na **Souhrn** dialogové okno, klikněte na tlačítko **Další**.
7. Na **dokončení** dialogové okno, klikněte na tlačítko **Zavřít**.
8. Na panelu nástrojů v horní části klikněte na tlačítko **nasadit**.
   
    ![Konfigurace Windows Hello pro firmy](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Konfigurace profilu certifikátu
Pokud používáte ověřování pomocí certifikátů pro místní ověřování, budete muset nakonfigurovat a nasadit profil certifikátu. Tato úloha vyžaduje, abyste nastavili serveru NDES a role lokality bodu registrace certifikátu v portálu System Center Configuration Manager. Další podrobnosti najdete v tématu [požadavky na profily certifikátů v nástroji Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Otevřete **System Center Configuration Manager**a potom přejděte na **prostředky a kompatibilita > Nastavení dodržování předpisů > přístup k prostředkům společnosti > profily certifikátů**.
2. Vyberte šablonu, která obsahuje čipové karty přihlášení rozšířené použití klíče (EKU).

Na **zápis SCEP** stránky profilu certifikátu, je třeba vybrat **instalovat do služby Passport pro Work jinak dojde k selhání** jako **zprostředkovatele úložiště klíčů**.

## <a name="next-steps"></a>Další postup
* [Windows 10 pro firmy: Možnosti, jak používat zařízení pro práci](active-directory-azureadjoin-windows10-devices-overview.md)
* [Rozšíření možností cloudu u zařízení s Windows 10 prostřednictvím služby Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Ověření bez hesla](active-directory-azureadjoin-passport.md)
* [Další informace o scénářích použití pro službu Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Připojení zařízení k doméně služby Azure AD ve Windows 10 – ukázky z praxe](active-directory-azureadjoin-devices-group-policy.md)
* [Nastavení služby Azure AD Join](active-directory-azureadjoin-setup.md)

