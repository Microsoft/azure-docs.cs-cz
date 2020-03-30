---
title: Eskalovat oprávnění privátního cloudu
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje, jak eskalovat oprávnění v privátním cloudu pro funkce správy v programu vCenter.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d06f3e8be449e7050c65c75339a0cff6efe19e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025312"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalovat oprávnění virtuálního cloudu privátního cloudu z portálu CloudSimple

Pro přístup správce k privátnímu cloudu vCenter můžete dočasně eskalovat vaše oprávnění CloudSimple.  Pomocí zvýšených oprávnění můžete instalovat řešení VMware, přidávat zdroje identit a spravovat uživatele.

Noví uživatelé mohou být vytvořeni v doméně spřimi vCenter a získat přístup k vCenter.  Když vytváříte nové uživatele, přidejte je do cloudových jednoduchých skupin pro přístup k vCenter.  Další informace naleznete v [tématu CloudSimple Private Cloud model oprávnění vMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Neprovávejte žádné změny konfigurace pro součásti správy. Akce provedené během eskalovaného privilegovaného stavu mohou nepříznivě ovlivnit systém nebo způsobit, že systém nebude k dispozici.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="escalate-privileges"></a>Zvýšení úrovně oprávnění

1. Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **Prostředky,** vyberte privátní cloud, pro který chcete eskalovat oprávnění.

3. V dolní části stránky Souhrn v části **Změnit oprávnění vSphere**klepněte na **tlačítko Eskalovat**.

    ![Změnit oprávnění vSphere](media/escalate-private-cloud-privilege.png)

4. Vyberte typ uživatele vSphere.  Eskalovat lze pouze `CloudOwner@cloudsimple.local` místní uživatel.

5. V rozevíracím souboru vyberte časový interval eskalace. Zvolte nejkratší období, které vám umožní úkol dokončit.

6. Zaškrtnutím políčka potvrďte, že rizikům rozumíte.

    ![Dialogové okno Eskalovat oprávnění](media/escalate-private-cloud-privilege-dialog.png)

7. Klikněte na tlačítko **OK**.

8. Proces eskalace může trvat několik minut. Jakmile budete hotovi, klikněte na **OK**.

Eskalace oprávnění začíná a trvá až do konce vybraného intervalu.  K privátnímu cloudu vCenter se můžete přihlásit a dělat úkoly správy.

> [!IMPORTANT]
> Eskalovaná oprávnění může mít pouze jeden uživatel.  Před eskalací oprávnění jiného uživatele je nutné deeskalovat oprávnění uživatele.

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze do *skupiny Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* *nebo, Cloud-Global-VM-Admin-Group*.  Uživatelé připřidání do *skupiny Administrators* budou automaticky odebráni.  K přihlášení k webovému uživatelskému rozhraní vSphere je nutné přidat pouze účty služeb a účty služeb *administrators* a účty služeb.

## <a name="extend-privilege-escalation"></a>Rozšířit eskalaci oprávnění

Pokud k dokončení úkolů potřebujete více času, můžete prodloužit dobu eskalace oprávnění.  Zvolte další eskalační časový interval, který umožňuje dokončit úkoly správy.

1. Na **portálu Resources** > **Private Clouds** na portálu CloudSimple vyberte privátní cloud, pro který chcete rozšířit eskalaci oprávnění.

2. V dolní části karty Souhrn klepněte na **tlačítko Rozšířit eskalaci oprávnění**.

    ![Rozšířit eskalaci oprávnění](media/de-escalate-private-cloud-privilege.png)

3. V rozevíracím souboru vyberte eskalační časový interval. Zkontrolujte nový čas ukončení eskalace.

4. Chcete-li prodloužit interval, klepněte na tlačítko **Uložit.**

## <a name="de-escalate-privileges"></a>Deeskalovat oprávnění

Po dokončení úkolů správy byste měli de-eskalovat svá oprávnění.  

1. Na **Resources** > **portálu** Resources Private Clouds na portálu CloudSimple vyberte privátní cloud, pro který chcete deeskalovat oprávnění.

2. Klepněte na tlačítko **De-escalate**.

3. Klikněte na tlačítko **OK**.

> [!IMPORTANT]
> Chcete-li se vyhnout chybám, odhlaste se z vCenter a znovu se přihlaste po de-eskalující oprávnění.

## <a name="next-steps"></a>Další kroky

* [Nastavení zdrojů identit vCenter pro použití služby Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Instalace řešení zálohování pro [zálohování virtuálních počítačů](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)