---
title: Mapování atributů v Azure AD Connect synchronizace cloudu
description: Tento článek popisuje, jak používat funkci synchronizace cloudu Azure AD Connect k mapování atributů.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555206"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Mapování atributů v Azure AD Connect synchronizace cloudu

Pomocí funkce synchronizace cloudu služby Azure Active Directory (Azure AD) se můžete připojit k mapování atributů mezi místními objekty uživatelů nebo skupin a objekty ve službě Azure AD. Tato funkce se přidala do konfigurace synchronizace cloudu.

Mapování výchozích atributů můžete přizpůsobit (změnit, odstranit nebo vytvořit) podle vašich obchodních potřeb. Seznam atributů, které se synchronizují, najdete v tématu o [atributech synchronizovaných na Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Pochopení typů mapování atributů
Mapování atributů vám umožní řídit, jak se ve službě Azure AD naplní atributy. Azure AD podporuje čtyři typy mapování:

- **Direct**: atribut target se naplní hodnotou atributu propojeného objektu ve službě Active Directory.
- **Konstanta**: cílový atribut je naplněný konkrétním řetězcem, který zadáte.
- **Výraz**: cílový atribut je vyplněný na základě výsledku výrazu podobného skriptu. Další informace najdete v tématu [zápis výrazů pro mapování atributů v Azure Active Directory](reference-expressions.md).
- **None**: cílový atribut je ponechán beze změny. Nicméně pokud cílový atribut je někdy prázdný, je vyplněný výchozí hodnotou, kterou určíte.

Spolu s těmito základními typy mapování vlastních atributů podporují koncept volitelného přiřazení *výchozí* hodnoty. Výchozí přiřazení hodnoty zajišťuje, že cílový atribut je naplněný hodnotou, pokud Azure AD nebo cílový objekt nemá hodnotu. Nejběžnější konfigurací je ponecháno toto prázdné.

## <a name="understand-properties-of-attribute-mapping"></a>Pochopení vlastností mapování atributů

Společně s vlastností Type mapování atributů podporují následující atributy:

- **Zdrojový atribut**: atribut uživatele ze zdrojového systému (příklad: Active Directory).
- **Cílový atribut**: atribut uživatele v cílovém systému (příklad: Azure Active Directory).
- **Výchozí hodnota, pokud je null (volitelné)**: hodnota, která se předává cílovému systému, pokud má zdrojový atribut hodnotu null. Tato hodnota se zřídí jenom při vytvoření uživatele. Při aktualizaci stávajícího uživatele se nebude zřizovat.  
- **Použít toto mapování**:
  - **Always**: Toto mapování použijte pro akce vytvoření uživatele i aktualizace.
  - **Pouze během vytváření**: použít toto mapování pouze při akcích vytváření uživatele.

> [!NOTE]
> Tento článek popisuje, jak použít Azure Portal k mapování atributů.  Informace o použití Microsoft Graph naleznete v tématu [transformace](how-to-transformation.md).

## <a name="add-an-attribute-mapping"></a>Přidat mapování atributu

Chcete-li použít novou funkci, postupujte takto:

1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
2.  Vyberte **Azure AD Connect**.
3.  Vyberte **Spravovat cloudovou synchronizaci**.

    ![Snímek obrazovky zobrazující odkaz pro správu synchronizace cloudu](media/how-to-install/install-6.png)

4. V části **Konfigurace** vyberte svou konfiguraci.
5. Vyberte **kliknutím upravit mapování**.  Tento odkaz otevře obrazovku **mapování atributů** .

    ![Snímek obrazovky, který zobrazuje odkaz pro přidání atributů.](media/how-to-attribute-mapping/mapping-6.png)

6.  Vyberte **Přidat atribut**.

    ![Snímek obrazovky, který zobrazuje tlačítko pro přidání atributu společně se seznamy atributů a mapování typů.](media/how-to-attribute-mapping/mapping-1.png)

7. Vyberte typ mapování. V tomto příkladu používáme **výraz**.
8. Do pole zadejte výraz. V tomto příkladu používáme `Replace([mail], "@contoso.com", , ,"", ,)` .
9. Zadejte cílový atribut. V tomto příkladu používáme **ExtensionAttribute15**.
10. Vyberte, kdy se má toto mapování použít, a pak vyberte **použít**.

    ![Snímek obrazovky, který zobrazuje pole s výplní pro vytvoření mapování atributu](media/how-to-attribute-mapping/mapping-2a.png)

11. Zpět na obrazovce **mapování atributů** by se mělo zobrazit vaše nové mapování atributů.  
12. Vyberte **Uložit schéma**.

    ![Snímek obrazovky, na kterém se zobrazuje tlačítko Uložit schéma](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Otestování mapování atributů

K otestování mapování atributů můžete použít [zřizování na vyžádání](how-to-on-demand-provision.md): 

1. Na portálu Azure Portal vyberte **Azure Active Directory**.
2. Vyberte **Azure AD Connect**.
3. Vyberte **Spravovat zřizování**.
4. V části **Konfigurace** vyberte svou konfiguraci.
5. V části **ověřit** vyberte tlačítko **zřídit uživatele** . 
6. Na obrazovce **zřízení na vyžádání** zadejte rozlišující jméno uživatele nebo skupiny a klikněte na tlačítko **zřídit** . 

   Na obrazovce se zobrazí, že zřizování probíhá.

   ![Snímek obrazovky zobrazující probíhající zřizování](media/how-to-attribute-mapping/mapping-4.png)

8. Po dokončení zřizování se zobrazí obrazovka po úspěchu se čtyřmi zelenými značkami zaškrtnutí. 

   V části **provést akci** vyberte **Zobrazit podrobnosti**. Na pravé straně byste měli vidět, že je nový atribut synchronizovaný a použije se výraz.

   ![Snímek obrazovky zobrazující podrobnosti o úspěchu a exportu](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Další kroky

- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
- [Zápis výrazů pro mapování atributů](reference-expressions.md)
- [Atributy synchronizované se službou Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
