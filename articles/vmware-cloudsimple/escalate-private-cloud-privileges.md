---
title: Eskalace oprávnění privátního cloudu služby AVS – řešení Azure VMware pomocí služby AVS
description: Popisuje, jak eskalovat oprávnění v privátním cloudu služby AVS pro funkce správy v vCenter.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 211960af359e19f93afef58162c5b09ae1d9b23f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025312"
---
# <a name="escalate-avs-private-cloud-vcenter-privileges-from-the-avs-portal"></a>Eskalace oprávnění ke vCenter privátního cloudu služby AVS z portálu pro funkci AVS

Pro přístup pro správu vašeho privátního cloudového vCenter služby AVS můžete dočasně zvýšit svoje oprávnění ke službě AVS. Pomocí zvýšených oprávnění můžete nainstalovat řešení VMware, přidat zdroje identit a spravovat uživatele.

V doméně vCenter SSO se dají vytvářet noví uživatelé a mít přístup k vCenter. Když vytváříte nové uživatele, přidejte je do skupin služby AVS Builtin pro přístup k serveru vCenter. Další informace najdete v tématu [model oprávnění privátního cloudu služby AVS pro VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Pro součásti pro správu neprovádějte žádné změny konfigurace. Akce provedené během Eskalace privilegovaného stavu můžou negativně ovlivnit váš systém nebo můžou způsobit, že váš systém nebude k dispozici.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Zvýšení úrovně oprávnění

1. Přístup k [portálu AVS](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** , vyberte privátní cloud služby AVS, pro který chcete zvýšit oprávnění.

3. V dolní části stránky Souhrn v části **změnit oprávnění vSphere**klikněte na **Eskalace**.

    ![Změnit oprávnění vSphere](media/escalate-private-cloud-privilege.png)

4. Vyberte typ uživatele vSphere. Lze eskalovat pouze `CloudOwner@cloudsimple.local` místního uživatele.

5. Z rozevíracího seznamu vyberte časový interval eskalace. Vyberte nejkratší období, které vám umožní dokončit úlohu.

6. Zaškrtnutím políčka potvrďte, že rozumíte rizikům.

    ![Dialogové okno eskalace oprávnění](media/escalate-private-cloud-privilege-dialog.png)

7. Klikněte na **OK**.

8. Proces eskalace může trvat několik minut. Jakmile budete hotovi, klikněte na **OK**.

Eskalace oprávnění začíná a trvá až do konce vybraného intervalu. Abyste mohli provádět úlohy správy, můžete se přihlásit do svého privátního cloudového vCenter služby AVS.

> [!IMPORTANT]
> Pouze jeden uživatel může mít povýšené oprávnění. Než budete moct zvýšit oprávnění jiného uživatele, musíte zrušit jeho eskalaci.

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze *ke skupině*Cloud-Owner-Group *-Global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin* -Group nebo, *Cloud-Global-VM-admin-Group*.  Uživatelé přidaní do skupiny *správců* se automaticky odeberou.  Pouze účty služeb musí být přidány do skupiny *Administrators* a účty služeb nesmí být použity pro přihlášení k WEBOVÉmu uživatelskému rozhraní vSphere.

## <a name="extend-privilege-escalation"></a>Prodloužení eskalace oprávnění

Pokud k dokončení svých úkolů potřebujete další čas, můžete zvýšit dobu eskalace oprávnění. Vyberte další časový interval eskalace, který vám umožní dokončit úlohy správy.

1. V části **prostředky** > **privátní cloudy služby AVS** na portálu služby AVS vyberte privátní cloud, pro který chcete zvýšit eskalaci oprávnění.

2. V dolní části karty Souhrn klikněte na možnost **Zvětšit eskalaci oprávnění**.

    ![Prodloužení eskalace oprávnění](media/de-escalate-private-cloud-privilege.png)

3. Z rozevíracího seznamu vyberte časový interval eskalace. Zkontrolujte nový čas ukončení eskalace.

4. Kliknutím na **Uložit** rozšíříte interval.

## <a name="de-escalate-privileges"></a>Oprávnění ke zrušení eskalace

Po dokončení úloh správy byste měli zrušit eskalaci vašich oprávnění. 

1. V části **prostředky** > **privátní cloudy služby AVS** na portálu služby AVS vyberte privátní cloud, pro který chcete zrušit zvýšení oprávnění.

2. Klikněte na **de-eskalace**.

3. Klikněte na **OK**.

> [!IMPORTANT]
> Abyste se vyhnuli jakýmkoli chybám, odhlaste se od vCenter a znovu se přihlaste po oprávněních ke zrušení eskalace.

## <a name="next-steps"></a>Další kroky

* [Nastavení zdrojů identity vCenter pro používání služby Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Instalace řešení zálohování pro [zálohování virtuálních počítačů s úlohou](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)