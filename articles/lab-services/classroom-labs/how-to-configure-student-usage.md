---
title: Konfigurovat nastavení použití v testovacím prostředím v učebnách Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat počet uživatelů pro testovací prostředí, je registrován v testovacím, řídí počet hodin, můžete použít virtuální počítač a další.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142346"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurace nastavení použití a zásady
Tento článek popisuje, jak nakonfigurovat počet uživatelů pro testovací prostředí, je registrován v testovacím, řídí počet hodin, můžete použít virtuální počítač a další. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Zadejte počet uživatelů povolený do testovacího prostředí

1. Vyberte **Usage policy** (Zásady používání). 
2. V nastavení **Usage policy** (Zásady používání) zadejte **počet uživatelů**, kteří mohou testovací prostředí používat.
3. Vyberte **Save** (Uložit). 

    ![Zásady používání](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>Poslat odkaz registrace uživatele

1. Přepněte na zobrazení **Dashboard** (Řídicí panel). 
2. Vyberte dlaždici **User registration** (Registrace uživatelů).

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. Studenti pomocí odkazu na stránku, která pomáhá s registrací v testovacím. 

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/registration-link.png)
2. V dialogovém okně **User registration** (Registrace uživatelů) vyberte **Close** (Zavřít). 

## <a name="view-users-registered-with-the-lab"></a>Zobrazení uživatelů zaregistrovaných do testovacího prostředí

Vyberte **uživatelé** v nabídce vlevo, pokud chcete zobrazit seznam uživatelů zaregistrované testovací prostředí. 

![Seznam uživatelů zaregistrovaného testovacího prostředí](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Nastavení kvót na uživatele

1. Vyberte **uživatelská nastavení** v nabídce vlevo.
2. Vyberte **Quta uživatele** dlaždici. 
3. Vyberte **omezit počet hodin, může uživatel používat virtuální počítač**. 
4. Pro **kolik hodin chcete poskytnout všem uživatelům?**, zadejte počet hodin a vyberte **Uložit**. 

    ![Počet hodin na uživatele](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Zobrazí na počet hodin **kvóty uživatele** nyní dlaždici. 

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="manage-user-vms"></a>Správa virtuálních počítačů uživatele
Jakmile studenty registru pomocí Azure Lab Services pomocí registrace propojení za předpokladu, uvidíte virtuální počítače přiřazené pro studenty na **virtuálních počítačů** kartu. 

![Virtuální počítače přiřazené pro studenty](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Můžete provádět následující úlohy na studenta virtuálního počítače: 

- Zastavení virtuálního počítače, pokud je virtuální počítač spuštěný. 
- Spuštění virtuálního počítače, pokud je virtuální počítač zastavený. 
- Připojte se k virtuálnímu počítači. 
- Odstranění virtuálního počítače. 
- Zobrazí počet hodin, že uživatelé používat virtuální počítač. 


## <a name="next-steps"></a>Další postup
Začínáme s nastavením testovacího prostředí pomocí Azure Lab Services:

- [Nastavení testovacího prostředí v učebně](how-to-manage-classroom-labs.md)
- [Nastavení testovacího prostředí](../tutorial-create-custom-lab.md)
