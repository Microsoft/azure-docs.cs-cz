---
title: Pomocí portálu správce v zásobníku Azure | Microsoft Docs
description: Jako operátor zásobník Azure zjistěte, jak pomocí portálu správce.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: mabrigg
ms.openlocfilehash: 673b1144fe927e0619f5f8638d7e8ce9a181f48c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248516"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Pomocí portálu správce v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Existují dva portály v zásobníku Azure; Správce portálu a portálu user portal (někdy označovány jako *klienta* portálu.) Jako operátor zásobník Azure můžete na portálu správce pro každodenní správu a operace Azure zásobníku.

## <a name="access-the-administrator-portal"></a>Přístup k portálu správce

Pro prostředí pro vývoj kit, musíte nejprve se ujistěte, že můžete [připojit k hostiteli development kit](azure-stack-connect-azure-stack.md) prostřednictvím připojení ke vzdálené ploše nebo virtuální privátní sítě (VPN).

Pro přístup k portálu správce, přejděte na adresu URL portálu a přihlášení pomocí přihlašovacích údajů Azure zásobníku operátor. Pro integrovaný systém na portálu, které se liší podle adresy URL na základě název oblasti a externí plně kvalifikovaný název domény (FQDN) vašeho nasazení Azure zásobníku.

| Prostředí | Adresa URL portálu správce |   
| -- | -- | 
| Development kit| https://adminportal.local.azurestack.external  |
| Integrované systémy | https://adminportal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt; | 
| | |

 ![Portál správce](media/azure-stack-manage-portals/image1.png)

Na portálu správce může provádět akce, jako:

* Správa infrastruktury (včetně stavu systému, aktualizace, kapacity atd.)
* Naplnění webu Marketplace
* Vytvořte odběr pro uživatele
* Vytvoření plánu a nabídky

**Rychlý úvodní kurz** dlaždice obsahuje odkazy na online dokumentaci pro běžné úkoly.

I když má operátor můžete vytvářet prostředky jako virtuální počítače, virtuální sítě a účty úložiště na portálu správce, měli byste [přihlásit k portálu user portal](user/azure-stack-use-portal.md) při vytváření a testování prostředky.

>[!NOTE]
>**Vytvoření virtuálního počítače** má odkaz na dlaždici rychlý úvodní kurz, můžete vytvořit virtuální počítač na portálu správce, ale to je určené jenom k ověření zásobníku Azure po prvním nasazení.

## <a name="understand-subscription-behavior"></a>Porozumět chování předplatného

Je jenom jedno předplatné, které jsou k dispozici pro použití z portálu správce. Toto předplatné je *výchozí předplatné zprostředkovatele*. Nemůžete přidat žádné jiné předplatné a použít ji v portálu správce.

Jako operátor zásobník Azure můžete přidat předplatné pro uživatele (včetně sebe) z portálu správce. Uživatelé (včetně sebe) můžete přístup a použít tyto odběry z **uživatele** portálu. Portál user portal však neposkytuje přístup k některé z možností pro správu nebo provozní portálu správce.

Portály uživatelů a správců jsou zajišťované samostatné instance nástroje Azure Resource Manager. Z důvodu toto oddělení Resource Manager odběry nepřecházejí portálů. Například pokud vás, jakožto Obsluha zásobník Azure přihlásit k portálu user portal, můžete přístup k *výchozí předplatné zprostředkovatele*. I když nemáte přístup k žádné funkce správy, můžete vytvářet odběry pro sebe z nabídky veřejné. Tak dlouho, dokud jste přihlášení k portálu user portal jsou považovány za uživatele klienta.

  >[!NOTE]
  >Ve vývojovém prostředí sady Pokud uživatel patří do stejného adresáře klienta jako operátor zásobník Azure mohou nejsou blokovány v přihlášení k portálu správce. Však nemají přístup k některé z funkce správy. Z portálu správce, budou také nelze přidat předplatná nebo přístup poskytuje, které jsou k dispozici na ně v portálu user portal.

## <a name="administrator-portal-tips"></a>Tipy pro správce portálu

### <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete vybrat **úprava řídicího panelu** změnit výchozí řídicí panely, nebo vyberte **novým řídicím panelem** přidat vlastní řídicí panel. Dlaždice můžete snadno přidat na řídicí panel. Například můžete vybrat **nový**, klikněte pravým tlačítkem na **nabízí + plány**a potom vyberte **připnout na řídicí panel**.

### <a name="quick-access-to-online-documentation"></a>Rychlý přístup k online dokumentaci

Pro přístup k dokumentaci operátor zásobník Azure, naleznete v nápovědě a podporují ikonu (otazník) v pravém horním rohu portálu správce. Přesuňte kurzor na ikonu a vyberte **Nápověda a podpora**.

### <a name="quick-access-to-help-and-support"></a>Rychlý přístup k nápovědě a podpoře

Pokud v pravém horním rohu portálu správce, vyberte ikonu nápovědy a podpory (otazník) a potom vyberte **nová žádost o podporu**, nastat jedna následující výsledky:

- Pokud používáte integrovaný systém, otevře tato akce lokality, kde můžete přímo otevřít lístek podpory s Microsoft podporu služby zákazníkům (CSS). Odkazovat na [kde získat podporu](azure-stack-manage-basics.md#where-to-get-support) pochopit, kdy by měli přejít prostřednictvím podpory společnosti Microsoft nebo podporu dodavatele hardwaru výrobce (OEM).
- Pokud používáte sadě pro vývoj, otevře tato akce webu fóra Azure zásobníku přímo. Tato fóra jsou pravidelně sledovat. Protože development kit je prostředí pro testování, neexistuje oficiální podpora nabízeným přes Microsoft CSS.

## <a name="next-steps"></a>Další postup

- [Oblast správy v Azure zásobníku](azure-stack-region-management.md)
