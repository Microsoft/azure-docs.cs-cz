---
title: Správa účtu Azure Automation
description: Tento článek popisuje, jak spravovat konfiguraci vašeho účtu Automation, jako je chybná konfigurace, odstranění nebo obnovení certifikátu.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5fbdccf4e14ce1201b21f0490e9c890c77c3e2f0
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577751"
---
# <a name="manage-azure-automation-account"></a>Správa účtu Azure Automation
V určitém okamžiku před vypršením platnosti účtu Automation musíte obnovit certifikát. Pokud se domníváte, že zabezpečení účtu Spustit jako je ohrožené, můžete ho odstranit a vytvořit znovu. Tato část popisuje, jak tyto operace provést.

## <a name="cert-renewal"></a>Obnovení certifikátu podepsaného svým držitelem
Platnost certifikátu podepsaného svým držitelem, který jste vytvořili pro účet Spustit jako, vyprší jeden rok od data jeho vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Když ho obnovíte, aktuální platný certifikát se uchová, aby se zajistilo, že to negativně neovlivní runbooky ověřované účtem Spustit jako, které jsou právě ve frontě nebo aktivně spuštěné. Certifikát zůstane platný až do data vypršení jeho platnosti.

> [!NOTE]
> Pokud jste svůj účet Automation Spustit jako nakonfigurovali k používání certifikátu vydaného podnikovou certifikační autoritou a použijete tuto možnost, tento podnikový certifikát se nahradí certifikátem podepsaným svým držitelem.

Pokud chcete certifikát obnovit, postupujte takto:

1. Na webu Azure Portal otevřete účet Automation.

2. V okně **Účet Automation** 
3. v podokně **Vlastnosti účtu** v části **Nastavení účtu** vyberte **Účty Spustit jako**.

    ![Podokno vlastností účtu Automation](media/automation-manage-account/automation-account-properties-pane.png)
3. Na stránce vlastností **Účty Spustit jako** vyberte účet Spustit jako nebo účet Spustit jako pro Classic, pro který chcete obnovit certifikát.

4. V podokně **Vlastnosti** vybraného účtu klikněte na **Obnovit certifikát**.

    ![Obnovení certifikátu pro účet Spustit jako](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. Zatímco se certifikát obnovuje, můžete průběh sledovat v nabídce v části **Oznámení**.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Odstranění účet Spustit jako nebo Spustit jako pro Classic
Tato část popisuje, jak odstranit a znovu vytvořit účet Spustit jako nebo účet Spustit jako pro Classic. Při provedení této akce se účet Automation uchová. Odstraněný účet Spustit jako nebo účet Spustit jako pro Classic můžete znovu vytvořit na webu Azure Portal.

1. Na webu Azure Portal otevřete účet Automation.

2. Na stránce **Účet Automation** vyberte **Účty Spustit jako**.

3. Na stránce vlastností **Účty Spustit jako** vyberte účet Spustit jako nebo účet Spustit jako pro Classic, který chcete odstranit. Potom v podokně **Vlastnosti** vybraného účtu klikněte na **Odstranit**.

 ![Odstranění účtu Spustit jako](media/automation-manage-account/automation-account-delete-runas.png)

4. Zatímco se účet odstraňuje, můžete průběh sledovat v nabídce v části **Oznámení**.

5. Účet po odstranění můžete znovu vytvořit na stránce vlastností **Účty Spustit jako** výběrem možnosti Vytvořit v části **Účet Spustit jako pro Azure**.

 ![Znovuvytvoření účtu Automation Spustit jako](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>Chybná konfigurace
Může se stát, že se během prvotního nastavení nesprávně vytvoří nebo později odstraní některá z položek konfigurace nezbytných pro správné fungování účtu Spustit jako nebo Spustit jako pro Classic. Mezi tyto položky patří:

* Asset certifikátu
* Asset připojení
* Účet Spustit jako byl odebrán z role přispěvatele
* Instanční objekt nebo aplikace v Azure AD

V předchozí a dalších instancích chybné konfigurace účet Automation zjistí změny a v podokně vlastností **Účty Spustit jako** příslušného účtu zobrazí stav *Nedokončeno*.

![Nedokončená konfigurace účtu Spustit jako](media/automation-manage-account/automation-account-runas-incomplete-config.png)

Pokud vyberete tento účet Spustit jako, v podokně **Vlastnosti** účtu se zobrazí následující chybová zpráva:

![Zpráva upozornění o nedokončené konfiguraci účtu Spustit jako](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png).

Tyto potíže s účtem Spustit jako můžete rychle vyřešit jeho odstraněním a znovuvytvořením.

## <a name="next-steps"></a>Další postup
* Další informace o objektech služby najdete v článku [Objekty aplikací a hlavní objekty služeb](../active-directory/develop/app-objects-and-service-principals.md).
* Další informace o řízení přístupu na základě rolí ve službě Azure Automation najdete v článku [Řízení přístupu na základě rolí ve službě Azure Automation](automation-role-based-access-control.md).
* Další informace o certifikátech a službách Azure najdete v článku [Přehled certifikátů pro Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).