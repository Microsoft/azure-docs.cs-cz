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
ms.openlocfilehash: 63dc18c522a1e2e3b03bdf806945e0be67774b18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81870475"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalace privátního cloudu oprávnění vCenter z portálu CloudSimple

Pro přístup pro správu ke svému privátnímu cloudu vCenter můžete dočasně zvýšit svoje oprávnění CloudSimple.  Pomocí zvýšených oprávnění můžete nainstalovat řešení VMware, přidat zdroje identit a spravovat uživatele.

V doméně vCenter SSO se dají vytvářet noví uživatelé a mít přístup k vCenter.  Když vytváříte nové uživatele, přidejte je do CloudSimple Builtin Groups pro přístup k serveru vCenter.  Další informace najdete v tématu [model oprávnění privátního cloudu CloudSimple pro VMware vCenter](https://docs.microsoft.com/azure/vmware-cloudsimple/learn-private-cloud-permissions/).

> [!CAUTION]
> Pro součásti pro správu neprovádějte žádné změny konfigurace. Akce provedené během Eskalace privilegovaného stavu můžou negativně ovlivnit váš systém nebo můžou způsobit, že váš systém nebude k dispozici.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Zvýšení úrovně oprávnění

1. Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** , vyberte privátní cloud, pro který chcete zvýšit oprávnění.

3. V dolní části stránky Souhrn v části **změnit oprávnění vSphere**klikněte na **Eskalace**.

    ![Změnit oprávnění vSphere](media/escalate-private-cloud-privilege.png)

4. Vyberte typ uživatele vSphere.  `CloudOwner@cloudsimple.local`Dá se eskalovat jenom místní uživatel.

5. Z rozevíracího seznamu vyberte časový interval eskalace. Vyberte nejkratší období, které vám umožní dokončit úlohu.

6. Zaškrtnutím políčka potvrďte, že rozumíte rizikům.

    ![Dialogové okno eskalace oprávnění](media/escalate-private-cloud-privilege-dialog.png)

7. Klikněte na **OK**.

8. Proces eskalace může trvat několik minut. Jakmile budete hotovi, klikněte na **OK**.

Eskalace oprávnění začíná a trvá až do konce vybraného intervalu.  Abyste mohli provádět úlohy správy, můžete se přihlásit do svého privátního cloudu vCenter.

> [!IMPORTANT]
> Pouze jeden uživatel může mít povýšené oprávnění.  Než budete moct zvýšit oprávnění jiného uživatele, musíte zrušit jeho eskalaci.

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze *ke skupině*Cloud-Owner-Group *-Global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin* -Group nebo, *Cloud-Global-VM-admin-Group*.  Uživatelé přidaní do skupiny *správců* se automaticky odeberou.  Pouze účty služeb musí být přidány do skupiny *Administrators* a účty služeb nesmí být použity pro přihlášení k WEBOVÉmu uživatelskému rozhraní vSphere.

## <a name="extend-privilege-escalation"></a>Prodloužení eskalace oprávnění

Pokud k dokončení svých úkolů potřebujete další čas, můžete zvýšit dobu eskalace oprávnění.  Vyberte další časový interval eskalace, který vám umožní dokončit úlohy správy.

1. V **Resources**  >  **privátních cloudech** prostředků na portálu CloudSimple vyberte privátní cloud, pro který chcete zvýšit eskalaci oprávnění.

2. V dolní části karty Souhrn klikněte na možnost **Zvětšit eskalaci oprávnění**.

    ![Prodloužení eskalace oprávnění](media/de-escalate-private-cloud-privilege.png)

3. Z rozevíracího seznamu vyberte časový interval eskalace. Zkontrolujte nový čas ukončení eskalace.

4. Kliknutím na **Uložit** rozšíříte interval.

## <a name="de-escalate-privileges"></a>Oprávnění ke zrušení eskalace

Po dokončení úloh správy byste měli zrušit eskalaci vašich oprávnění.  

1. V části **prostředky**  >  **privátní cloudy** na portálu CloudSimple vyberte privátní cloud, pro který chcete zrušit zvýšení oprávnění.

2. Klikněte na **de-eskalace**.

3. Klikněte na **OK**.

> [!IMPORTANT]
> Abyste se vyhnuli jakýmkoli chybám, odhlaste se od vCenter a znovu se přihlaste po oprávněních ke zrušení eskalace.

## <a name="next-steps"></a>Další kroky

* [Nastavení zdrojů identity vCenter pro používání služby Active Directory](https://docs.microsoft.com/azure/vmware-cloudsimple/set-vcenter-identity/)
* Instalace řešení zálohování pro [zálohování virtuálních počítačů s úlohou](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam/)