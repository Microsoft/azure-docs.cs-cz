---
title: Editor atributů zřizování cloudu Azure AD Connect
description: Tento článek popisuje, jak používat Editor atributů.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b7aa0bf8c82990a00b4e41041145a67ee2f02b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637123"
---
# <a name="azure-ad-connect-cloud-provisioning-attibute-mapping"></a>Azure AD Connect mapování atribut Cloud zřizování

Azure AD Connect zřízení cloudu zavedlo novou funkci, která vám umožní snadnou mapu atributů mezi místními objekty uživatelů nebo skupin a objekty ve službě Azure AD.  Tato funkce se přidala do konfigurace zřizování cloudu.

Výchozí mapování atributů můžete přizpůsobit podle potřeb vaší firmy. Takže můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nová mapování atributů.  Seznam synchronizovaných atributů naleznete v tématu [synchronizované atributy](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understanding-attribute-mapping-types"></a>Porozumění typům mapování atributů
Mapování atributů vám umožní řídit, jak se ve službě Azure AD naplní atributy.
Podporovány jsou čtyři různé typy mapování:

- **Direct** – cílový atribut se naplní hodnotou atributu propojeného objektu ve službě AD.
- **Constant** – cílový atribut je naplněný konkrétním řetězcem, který jste zadali.
- **Výraz** – cílový atribut je vyplněný na základě výsledku výrazu podobného skriptu.
  Další informace najdete v tématu [zápis výrazů pro mapování atributů](reference-expressions.md).
- **None** – cílový atribut zůstane beze změny. Nicméně pokud cílový atribut je někdy prázdný, je vyplněný výchozí hodnotou, kterou určíte.

Spolu s těmito čtyřmi základními typy jsou vlastní mapování atributů podporují koncept volitelného přiřazení **výchozí** hodnoty. Výchozí přiřazení hodnoty zajišťuje, že cílový atribut je naplněný hodnotou, pokud neexistuje hodnota ve službě Azure AD nebo v cílovém objektu. Nejběžnější konfigurací je ponecháno toto prázdné.

## <a name="understanding-attribute-mapping-properties"></a>Porozumění vlastnostem mapování atributů

V předchozí části jste již zavedli vlastnost typu mapování atributů.
Spolu s touto vlastností také mapování atributů podporuje následující atributy:

- **Zdrojový atribut** – atribut uživatele ze zdrojového systému (příklad: Active Directory).
- **Cílový atribut** – atribut uživatele v cílovém systému (příklad: Azure Active Directory).
- **Výchozí hodnota, pokud je null (volitelné)** – hodnota, která se předává cílovému systému, pokud má zdrojový atribut hodnotu null. Tato hodnota se zřídí jenom při vytvoření uživatele. Při aktualizaci stávajícího uživatele nebude zajištěna výchozí hodnota, pokud je null.  
- **Použít toto mapování**
  - **Vždy** – použít toto mapování na akce vytvoření a aktualizace uživatele
  - **Pouze během vytváření** – použít toto mapování pouze při akcích vytvoření uživatele.

> [!NOTE]
> Tento dokument popisuje, jak použít Azure Portal k mapování atributů.  Informace o použití grafu naleznete v tématu [transformace](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>Použití mapování atributů
Chcete-li použít novou funkci, postupujte podle následujících kroků.

 1.  V Azure Portal vyberte **Azure Active Directory**.
 2.  Vyberte **Azure AD Connect**.
 3.  Vyberte **Spravovat zřizování**.

   ![Spravovat zřizování](media/how-to-configure/manage1.png)
 
 4. V části **Konfigurace**vyberte svou konfiguraci.
 5. Vyberte **kliknutím upravit mapování**.  Tím se otevře obrazovka mapování atributů.

 ![Přidávání atributů](media/how-to-attribute-mapping/mapping6.png)
 6.  Klikněte na **Přidat atribut**.

 ![Typ mapování](media/how-to-attribute-mapping/mapping1.png)
 
 7. Vyberte **typ mapování**.  V tomto příkladu používáme výraz.
 8.  Do pole zadejte výraz.  V tomto příkladu používáme: `Replace([mail], "@contoso.com", , ,"", ,).`
 9.  Zadejte cílový atribut.  V tomto příkladu používáme ExtensionAttribute15.
 10. Vyberte, kdy se má použít, a potom klikněte na **použít** .
   
   ![Upravit mapování](media/how-to-attribute-mapping/mapping2a.png)
 11. Zpátky na obrazovce mapování atributů byste měli vidět vaše nové mapování atributů.  
 12. Klikněte na **Uložit schéma**.

 ![Uložit schéma](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>Otestování mapování atributů
K otestování mapování atributů můžete použít [zřizování na vyžádání](how-to-on-demand-provision.md).  Z okna 

1.  V Azure Portal vyberte **Azure Active Directory**.
2.  Vyberte **Azure AD Connect**.
3.  Vyberte **Spravovat zřizování**.
4. V části **Konfigurace**vyberte svou konfiguraci.
5. V části **ověřit** klikněte na tlačítko **zřídit uživatele** . 
6. Na obrazovce zřizování na vyžádání.  Zadejte **rozlišující jméno** uživatele nebo skupiny a klikněte na tlačítko **zřídit** .  
7. Po dokončení by se měla zobrazit obrazovka úspěšná a 4 zelená zaškrtávací políčka, která označují úspěšné zřízení.  
  ![Úspěch pro zřizování](media/how-to-attribute-mapping/mapping4.png)
1. V části **provést akci** klikněte na **Zobrazit podrobnosti**.  Na pravé straně by se měl zobrazit nový atribut syncrhonized a použit výraz.

  ![Provést akci](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>Další kroky

- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
- [Zápis výrazů pro mapování atributů](reference-expressions.md)
- [Synchronizované atributy](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)