---
title: Zvýšení úrovně oprávnění privátního cloudu – řešení VMware Azure podle CloudSimple
description: Popisuje, jak zvýšit oprávnění na vašem privátním cloudu pro funkce správy v systému vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332849"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Zvýšení úrovně oprávnění vCenter privátního cloudu z portálu CloudSimple 

Pro správcovský přístup k vCenter privátního cloudu můžete dočasně zvýšit vaši CloudSimple oprávnění.  Použitím zvýšených oprávnění, můžete nainstalovat řešení VMware, přidejte identita zdroje a spravovat uživatele.

Noví uživatelé se dají vytvářet v doméně jednotného přihlašování k serveru vCenter a poskytnut přístup k serveru vCenter.  Při vytváření nových uživatelů, přidejte je do skupiny builtin CloudSimple pro přístup k serveru vCenter.  Další informace najdete v tématu [CloudSimple privátního cloudu oprávnění modelu systému VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Neprovádějte žádné změny konfigurace pro součásti správy. Akce prováděné během eskalované privileged stavu může nepříznivě ovlivnit vašeho systému nebo může způsobit, že váš systém přestanou být dostupné.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Zvýšení úrovně oprávnění

1. Přístup [CloudSimple portál](access-cloudsimple-portal.md).

2. Otevřít **prostředky** vyberte privátní Cloud, pro které chcete ke zvýšení úrovně oprávnění.

3. U dolního okraje stránky v části Souhrn **změnit oprávnění vSphere**, klikněte na tlačítko **eskalací**.

    ![Změnit oprávnění vSphere](media/escalate-private-cloud-privilege.png)

4. Vyberte typ uživatele vSphere.  Pouze **CloudOwner@cloudsimple.local** místního uživatele může být rozšířena.

5. Z rozevíracího seznamu vyberte časový interval eskalací. Zvolte, které vám umožní dokončení úlohy nejkratší dobu.

6. Zaškrtnutím políčka potvrďte, že rozumíte rizika.

    ![Eskalovat dialogové okno oprávnění](media/escalate-private-cloud-privilege-dialog.png)

7. Klikněte na **OK**.

8. Eskalace proces může trvat několik minut. Jakmile budete hotovi, klikněte na **OK**.

Zvýšení úrovně oprávnění začíná a trvá až do konce vybraného intervalu.  K vCenter privátního cloudu se můžete přihlásit k provádění úloh správy.

> [!IMPORTANT]
> Jenom jeden uživatel může mít zvýšil oprávnění.  Zvýšení úrovně oprávnění uživatele můžete zrušit musí předtím, než můžete zvýšit oprávnění jiným uživatelem.

## <a name="extend-privilege-escalation"></a>Rozšíření zvýšení úrovně oprávnění

Pokud budete potřebovat další čas k dokončení úkolů, můžete rozšířit období eskalaci oprávnění.  Zvolte že další eskalovat časový interval, který umožňuje provádět úlohy správy.

1. Na **prostředky** > **privátních Cloudů** CloudSimple portálu vyberte privátní Cloud, pro který chcete rozšířit zvýšení úrovně oprávnění.

2. V dolní části Souhrn klikněte na tlačítko **rozšířit zvýšení úrovně oprávnění**.

    ![Rozšíření zvýšení úrovně oprávnění](media/de-escalate-private-cloud-privilege.png)

3. Z rozevíracího seznamu vyberte časový interval eskalací. Projděte si nový koncový čas eskalace.

4. Klikněte na tlačítko **Uložit** rozšířit interval.

## <a name="de-escalate-privileges"></a>Zrušení zvýšení oprávnění

Po dokončení vaší úlohy správy by měla předat zruší vaše oprávnění.  

1. Na **prostředky** > **privátních Cloudů** CloudSimple portálu vyberte privátní Cloud, pro kterou chcete zrušení zvýšení oprávnění.

2. Klikněte na tlačítko **zrušit eskalovat**.

3. Klikněte na **OK**.

> [!IMPORTANT]
> Aby se zabránilo chybám, odhlásit se ze serveru vCenter a přihlaste se znovu po zrušení eskalaci oprávnění.

## <a name="next-steps"></a>Další postup

* [Nastavení zdroje identity serveru vCenter pro použití služby Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Nainstalujte řešení zálohování do [zálohování virtuálních počítačů pracovního vytížení](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)