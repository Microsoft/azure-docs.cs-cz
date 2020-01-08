---
title: Eskalovat oprávnění privátního cloudu
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje, jak eskalovat oprávnění ve vašem privátním cloudu pro funkce správy v vCenter.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ee722f88f766178dd658600893b4497bf611be9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372767"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalace privátního cloudu oprávnění vCenter z portálu CloudSimple

Pro přístup pro správu ke svému privátnímu cloudu vCenter můžete dočasně zvýšit svoje oprávnění CloudSimple.  Pomocí zvýšených oprávnění můžete nainstalovat řešení VMware, přidat zdroje identit a spravovat uživatele.

V doméně vCenter SSO se dají vytvářet noví uživatelé a mít přístup k vCenter.  Když vytváříte nové uživatele, přidejte je do CloudSimple Builtin Groups pro přístup k serveru vCenter.  Další informace najdete v tématu [model oprávnění privátního cloudu CloudSimple pro VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Pro součásti pro správu neprovádějte žádné změny konfigurace. Akce provedené během Eskalace privilegovaného stavu můžou negativně ovlivnit váš systém nebo můžou způsobit, že váš systém nebude k dispozici.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Zvýšení úrovně oprávnění

1. Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** , vyberte privátní cloud, pro který chcete zvýšit oprávnění.

3. V dolní části stránky Souhrn v části **změnit oprávnění vSphere**klikněte na **Eskalace**.

    ![Změnit oprávnění vSphere](media/escalate-private-cloud-privilege.png)

4. Vyberte typ uživatele vSphere.  Lze eskalovat pouze `CloudOwner@cloudsimple.local` místního uživatele.

5. Z rozevíracího seznamu vyberte časový interval eskalace. Vyberte nejkratší období, které vám umožní dokončit úlohu.

6. Zaškrtnutím políčka potvrďte, že rozumíte rizikům.

    ![Dialogové okno eskalace oprávnění](media/escalate-private-cloud-privilege-dialog.png)

7. Klikněte na **OK**.

8. Proces eskalace může trvat několik minut. Jakmile budete hotovi, klikněte na **OK**.

Eskalace oprávnění začíná a trvá až do konce vybraného intervalu.  Abyste mohli provádět úlohy správy, můžete se přihlásit do svého privátního cloudu vCenter.

> [!IMPORTANT]
> Pouze jeden uživatel může mít povýšené oprávnění.  Než budete moct zvýšit oprávnění jiného uživatele, musíte zrušit jeho eskalaci.

## <a name="extend-privilege-escalation"></a>Prodloužení eskalace oprávnění

Pokud k dokončení svých úkolů potřebujete další čas, můžete zvýšit dobu eskalace oprávnění.  Vyberte další časový interval eskalace, který vám umožní dokončit úlohy správy.

1. V části **prostředky** > **privátní cloudy** na portálu CloudSimple vyberte privátní cloud, pro který chcete zvýšit eskalaci oprávnění.

2. V dolní části karty Souhrn klikněte na možnost **Zvětšit eskalaci oprávnění**.

    ![Prodloužení eskalace oprávnění](media/de-escalate-private-cloud-privilege.png)

3. Z rozevíracího seznamu vyberte časový interval eskalace. Zkontrolujte nový čas ukončení eskalace.

4. Kliknutím na **Uložit** rozšíříte interval.

## <a name="de-escalate-privileges"></a>Oprávnění ke zrušení eskalace

Po dokončení úloh správy byste měli zrušit eskalaci vašich oprávnění.  

1. V části **prostředky** > **privátní cloudy** na portálu CloudSimple vyberte privátní cloud, pro který chcete zrušit eskalaci oprávnění.

2. Klikněte na **de-eskalace**.

3. Klikněte na **OK**.

> [!IMPORTANT]
> Abyste se vyhnuli jakýmkoli chybám, odhlaste se od vCenter a znovu se přihlaste po oprávněních ke zrušení eskalace.

## <a name="next-steps"></a>Další kroky

* [Nastavení zdrojů identity vCenter pro používání služby Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Instalace řešení zálohování pro [zálohování virtuálních počítačů s úlohou](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)