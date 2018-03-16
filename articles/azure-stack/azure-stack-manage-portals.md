---
title: "Pomocí portálu správce v zásobníku Azure | Microsoft Docs"
description: "Jako operátor zásobník Azure zjistěte, jak pomocí portálu správce."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 34d9d207225327758d535a11c870019f3db31cb0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Pomocí portálu správce v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Existují dva portály v zásobníku Azure; Správce portálu a portálu user portal (někdy označovány jako *klienta* portálu). Jako operátor zásobník Azure můžete na portálu správce pro každodenní správu a operace Azure zásobníku. 

## <a name="access-the-administrator-portal"></a>Přístup k portálu správce

Pro prostředí pro vývoj kit, musíte nejprve se ujistěte, že můžete [připojit k hostiteli development kit](azure-stack-connect-azure-stack.md) prostřednictvím připojení ke vzdálené ploše nebo virtuální privátní sítě (VPN).

Pro přístup k portálu správce, přejděte na adresu URL portálu a přihlášení pomocí přihlašovacích údajů Azure zásobníku operátor. Pro integrovaný systém na portálu, které se liší podle adresy URL na základě název oblasti a externí plně kvalifikovaný název domény (FQDN) vašeho nasazení Azure zásobníku.

| Prostředí | Adresa URL portálu správce |   
| -- | -- | 
| Development kit| https://adminportal.local.azurestack.external  |
| Integrované systémy | https://adminportal. &lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt; | 
| | |

 ![Portál správce](media/azure-stack-manage-portals/image1.png)

Na portálu správce může provádět akce, jako:

* Správa infrastruktury (včetně stavu systému, aktualizace, kapacity atd.)
* Naplnění marketplace.
* Vytvoření plánu a nabídky
* Vytvořte odběr pro uživatele

V **rychlý úvodní kurz** dlaždici, existují odkazy na online dokumentaci pro běžné úkoly.
 
I když je možnost pro operátor k vytváření prostředků jako virtuální počítače, virtuální sítě a účty úložiště v portálu správce, měli byste [přihlásit k portálu user portal](user/azure-stack-use-portal.md) při vytváření a testování prostředky. ( **Vytvoření virtuálního počítače** má odkaz na dlaždici rychlý úvodní kurz, můžete vytvořit virtuální počítač na portálu správce, ale tento postup je pouze k ověření zásobníku Azure po počátečním nasazení.)

## <a name="subscription-behavior"></a>Chování předplatného
 
Je jenom jedno předplatné, které jsou dostupné na portálu správce. Toto předplatné je *výchozí předplatné zprostředkovatele*. Nemůžete přidat žádné jiné předplatné pro použití na portálu správce.

Jako operátor zásobník Azure můžete přidat předplatné pro uživatele (včetně sebe) z portálu správce. Uživatelé (včetně sebe), můžete přístup a použít tyto odběry z portálu user portal. Neposkytuje přístup k některé z možností pro správu nebo provozní portálu správce portálu user portal.

Portály uživatelů a správců jsou zajišťované samostatné instance nástroje Azure Resource Manager. Z důvodu oddělení Resource Manager odběry nepřecházejí portálů. Například pokud jste jako operátor zásobník Azure přihlásí k portálu user portal, nemáte přístup předplatného výchozí zprostředkovatel. Proto nemáte přístup k žádné funkce správy. Můžete vytvářet odběry pro sebe z veřejné nabídky, ale jsou považovány za uživatele klienta.

  >[!NOTE]
  Ve vývojovém prostředí sady Pokud uživatel patří do stejného adresáře klienta jako operátor zásobník Azure mohou nejsou blokovány v přihlášení k portálu správce. Však nemají přístup k některé z funkce správy. Z portálu správce, budou také nelze přidat předplatná nebo přístup poskytuje, které jsou jim zpřístupní v portálu user portal.

## <a name="administrator-portal-tips"></a>Tipy pro správce portálu

### <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete kliknout na **úprava řídicího panelu** změnit výchozí řídicí panely, nebo klikněte na tlačítko **novým řídicím panelem** přidat vlastní řídicí panely. Dlaždice můžete snadno přidat na řídicí panel. Například můžete kliknout na **nový**, klikněte pravým tlačítkem na **nabízí + plány**a potom klikněte na **připnout na řídicí panel**.

### <a name="quick-access-to-online-documentation"></a>Rychlý přístup k online dokumentaci

Pro přístup k dokumentaci operátor zásobník Azure, klikněte na ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce a pak klikněte na tlačítko **Nápověda a podpora**.

### <a name="quick-access-to-help-and-support"></a>Rychlý přístup k nápovědě a podpoře

Klikněte na ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce, a pak klikněte na tlačítko **nová žádost o podporu**, to nemá z následujících:

- Pokud používáte integrovaný systém, otevře tato akce lokality, kde můžete přímo otevřít lístek podpory s Microsoft podporu služby zákazníkům (CSS). Informace naleznete v sekci "Kde získat podporu" z [základy správy Azure zásobníku](azure-stack-manage-basics.md) pochopit, kdy by měli přejít prostřednictvím podpory společnosti Microsoft nebo podporu dodavatele hardwaru výrobce (OEM).
- Pokud používáte sadě pro vývoj, otevře tato akce webu fóra Azure zásobníku přímo. Tato fóra jsou pravidelně sledovat. Protože development kit je prostředí pro testování, neexistuje oficiální podpora nabízeným přes Microsoft CSS.

## <a name="next-steps"></a>Další postup

- [Oblast správy v Azure zásobníku](azure-stack-region-management.md)
