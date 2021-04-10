---
title: Definování globálního řízení přístupu uživatelů
description: Ve velkých organizacích můžou být uživatelská oprávnění složitá a můžou být určená globální strukturou organizace, a to i se standardní strukturou lokalit a zón.
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784284"
---
# <a name="define-global-access-control"></a>Definování globálního řízení přístupu

Ve velkých organizacích můžou být uživatelská oprávnění složitá a můžou být určená globální strukturou organizace, a to i se standardní strukturou lokalit a zón.

Aby bylo možné podporovat požadavky na uživatelská přístupová oprávnění, která jsou globální a složitější, můžete vytvořit globální obchodní topologii založenou na obchodních jednotkách, oblastech a lokalitách. Pak můžete pro tyto entity definovat oprávnění k přístupu uživatele.

Práce s nástroji Access Tools for Business Topology pomáhá organizacím implementovat strategie nulové důvěryhodnosti tím, že lépe ovládá, kde uživatelé spravují a analyzují zařízení v Azure Defenderu pro platformu IoT.

## <a name="about-access-groups"></a>O přístupových skupinách

Globální řízení přístupu se vytváří pomocí skupin přístupu uživatelů. Přístupové skupiny se skládají z pravidel, která uživatelům mají přístup ke konkrétním obchodním entitám. Práce se skupinami vám umožní řídit zobrazení a přístup k konfiguraci pro IoT pro konkrétní role uživatelů v příslušných obchodních jednotkách, oblastech a lokalitách.

Například umožňuje analytikům zabezpečení ze skupiny služby Active Directory přistupovat ke všem západním Evropským automobilovým a skleněným linkám, a to spolu s plastovou čárou v jedné oblasti.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagram skupiny služby Active Directory analytika zabezpečení":::

Před vytvořením přístupových skupin doporučujeme:

- Pečlivě nastavte svoji obchodní topologii. Další informace o obchodní topologii najdete v tématu [práce se zobrazeními map webu](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Naplánujte, které uživatele jsou přidruženy k vytvořeným skupinám přístupu. K dispozici jsou dvě možnosti pro přiřazení uživatelů ke skupinám:

  - **Přiřazení skupin skupin služby Active Directory**: Ověřte, že jste nastavili instanci služby Active Directory pro integraci s místní konzolou pro správu.
  
  - **Přiřazení místních uživatelů**: Ověřte, že jste vytvořili uživatele. Další informace najdete v tématu věnovaném [definování uživatelů](how-to-create-and-manage-users.md#define-users).

Uživatelům s oprávněními správce nelze přiřadit přístupové skupiny. Tito uživatelé mají ve výchozím nastavení přístup ke všem entitám obchodní topologie.

## <a name="create-access-groups"></a>Vytvoření skupin přístupu

Tato část popisuje, jak vytvořit přístupové skupiny. Výchozí globální obchodní jednotky a oblasti se vytvoří pro první skupinu, kterou vytvoříte. Výchozí entity můžete upravit při definování první skupiny.

Vytvoření skupin:

1. V místní nabídce místní konzoly pro správu vyberte **přístupové skupiny** .

2. Vyberte :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. V dialogovém okně **Přidat skupinu přístupu** zadejte název přístupové skupiny. Konzola podporuje 64 znaků. Přiřaďte název způsobem, který vám pomůže snadno odlišit tuto skupinu od ostatních skupin.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Dialogové okno Přidat skupinu přístupu, ve kterém vytvoříte přístupové skupiny.":::

3. Pokud se zobrazí možnost **přiřadit skupinu služby Active Directory** , můžete k této přístupové skupině přiřadit jednu skupinu uživatelů služby Active Directory.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Přiřaďte skupinu služby Active Directory v dialogovém okně vytvořit skupinu přístupu.":::

   Pokud se možnost nezobrazí a chcete do skupin přístupu zahrnout skupiny služby Active Directory, vyberte **nastavení systému**. V podokně **integrace** Definujte skupiny. Zadejte název skupiny přesně tak, jak se zobrazuje v konfiguracích služby Active Directory, a malými písmeny.

5. V podokně **Uživatelé** přiřaďte skupině tolik uživatelů, kolik jich vyžaduje. Můžete také přiřadit uživatele do různých skupin. Pokud pracujete tímto způsobem, musíte vytvořit a uložit skupinu přístupu a pravidla a potom uživatele přiřadit do skupiny z podokna **Uživatelé** .

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Spravujte role uživatelů a podle potřeby je přiřaďte.":::

6. V dialogovém okně **Přidat pravidla pro *název*** vytvořte pravidla v závislosti na požadavcích na přístup vaší obchodní topologie. Tady uvedené možnosti jsou založené na topologii, kterou jste navrhli v oknech **podnikového zobrazení** a **Správa webu** . 

   Na skupinu můžete vytvořit více než jedno pravidlo. Pokud pracujete se složitým rozstupným přístupem v několika lokalitách, může být nutné vytvořit více než jedno pravidlo pro každou skupinu. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Přidejte do svého systému pravidlo.":::

Vytvořená pravidla se zobrazí v dialogovém okně **Přidat skupinu přístupu** . Tam je můžete odstranit nebo upravit.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Zobrazení a úprava všech přístupových skupin z tohoto okna.":::

### <a name="about-rules"></a>O pravidlech

Při vytváření pravidel si pamatujte na následující informace:

- Pokud přístupová skupina obsahuje několik pravidel, logika pravidla agreguje všechna pravidla. Například pravidla používají a Logic, not nebo Logic.

- Aby bylo pravidlo úspěšně použito, je nutné přiřadit senzory do zón v okně **Správa lokality** .

- Můžete přiřadit pouze jeden prvek na jedno pravidlo. Můžete například přiřadit jednu organizační jednotku, jednu oblast a jednu lokalitu pro každé pravidlo. Vytvořte pro skupinu další pravidla, pokud například chcete, aby uživatelé v jedné skupině služby Active Directory měli přístup k různým obchodním jednotkám v různých oblastech.

- Pokud změníte entitu a tato změna ovlivní logiku pravidla, pravidlo se odstraní. Pokud se změny provedené u entity topologie projeví v logice pravidla, takže se odstraní všechna pravidla, zůstane skupina přístupu, ale uživatelé se nebudou moci přihlásit k místní konzole pro správu. Uživatelům se zobrazí oznámení, že se mají obrátit na správce.

- Pokud není vybraná žádná obchodní jednotka nebo oblast, budou mít uživatelé přístup ke všem definovaným obchodním jednotkám a oblastem.

## <a name="see-also"></a>Viz také

[O programu Defender pro uživatele konzoly IoT](how-to-create-and-manage-users.md)
