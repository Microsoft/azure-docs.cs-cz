---
title: Použití portálu správce ve službě Azure Stack | Dokumentace Microsoftu
description: Jako operátory Azure stacku zjistěte, jak pomocí portálu správce.
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
ms.date: 12/04/2018
ms.author: mabrigg
ms.openlocfilehash: 58856875fa7d7bb3ba63c489fb17790e68f99aec
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872182"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Použití portálu správce ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Existují dva portály ve službě Azure Stack; na portálu správce a na portálu user portal (někdy označovány jako *tenanta* portálu.) Jako operátory Azure stacku můžete použít portál správce pro každodenní správu a operace služby Azure Stack.

## <a name="access-the-administrator-portal"></a>Přístup k portálu správce

Pro vývojové prostředí sady, budete muset nejprve se ujistěte, že můžete [připojení k hostiteli development kit](azure-stack-connect-azure-stack.md) prostřednictvím připojení ke vzdálené ploše nebo virtuální privátní sítě (VPN).

Pro přístup k portálu správce, přejděte na adresu URL portálu a přihlášení pomocí přihlašovacích údajů operátory Azure stacku. Pro integrovaný systém portálu, k němuž adresy URL se liší podle oblasti názvu a externí plně kvalifikovaný název domény (FQDN) vašeho nasazení Azure Stack.

| Prostředí | Adresa URL portálu pro správce |   
| -- | -- | 
| Vývojová sada| https://adminportal.local.azurestack.external  |
| Integrované systémy | https://adminportal.&lt; *oblasti*&gt;.&lt; *Plně kvalifikovaný název domény*&gt; | 
| | |

 ![Na portálu správce](media/azure-stack-manage-portals/admin-portal.png)

Všimněte si, že výchozí časové pásmo pro všechna nasazení Azure Stack nastavený na koordinovaný univerzální čas (UTC). Časové pásmo můžete vybrat při instalaci Azure Stack, ale automaticky přejde na čas UTC jako výchozí během instalace.

Na portálu správce můžete dělat něco jako například:

* Správa infrastruktury (včetně stavu systému, kapacity, aktualizace atd.)
* Naplnění webu Marketplace
* Vytváření odběrů pro uživatele
* Vytvořit plány a nabídky

**Rychlý úvodní kurz** dlaždice obsahuje odkazy na online dokumentaci pro nejběžnější úkoly.

I když má operátor může vytvářet prostředky, jako je například virtuální počítače, virtuální sítě a účty úložišť na portálu pro správce, měli byste [přihlásit k portálu user portal](user/azure-stack-use-portal.md) vytvořit a otestovat prostředky.

>[!NOTE]
>**Vytvoření virtuálního počítače** má odkaz na dlaždici kurz rychlý start můžete vytvořit virtuální počítač na portálu pro správce, ale to je určené jenom k ověření služby Azure Stack po prvním nasazení.

## <a name="understand-subscription-behavior"></a>Pochopte chování předplatného

Existuje pouze jedno předplatné, které jsou k dispozici pro použití z portálu správce. Toto předplatné je *výchozí předplatné poskytovatele*. Nelze přidat další předplatná a jejich použití v portálu správce.

Jako operátory Azure stacku můžete přidat předplatná pro vaše uživatele (včetně sami) z portálu správce. Může přistupovat a používat tato předplatná z uživatelé (včetně sami) **uživatele** portálu. Portál user portal však neposkytuje přístup k některé z možností administrativních nebo provozních portálu správce.

Portály správce a uživatele se zálohují na samostatných instancí služby Azure Resource Manageru. Z důvodu toto oddělení Resource Manageru předplatná nepřecházejí portálů. Například pokud, jako operátory Azure stacku, přihlášení k portálu user portal, nelze získat přístup *výchozí předplatné poskytovatele*. I když nemáte přístup pro všechny funkce správy, můžete vytvořit předplatná sami z veřejné dostupným nabídkám. Tak dlouho, dokud jste přihlášení k portálu user portal se považují za tenanta uživatele.

  >[!NOTE]
  >Ve vývojovém prostředí sady Pokud uživatel patří do stejného adresáře tenanta jako operátory Azure stacku mu nejsou zablokovat přihlášení k portálu správce. Ale že nemůžete použít žádnou z funkce správy. Z portálu správce se také, nelze přidat předplatná nebo přístup nabídky, která jsou k dispozici na portálu user portal.

## <a name="administrator-portal-tips"></a>Tipy k portálu správce

### <a name="customize-the-dashboard"></a>Přizpůsobení řídicího panelu

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete vybrat **upravit řídicí panel** změnit výchozí řídicí panel, nebo vyberte **nový řídicí panel** přidáte vlastní řídicí panel. Dlaždice můžete snadno přidat na řídicí panel. Například můžete vybrat **+ vytvořit prostředek**, klikněte pravým tlačítkem na **nabízí + plány**a pak vyberte **připnout na řídicí panel**.

V některých případech se může zobrazit prázdný řídicí panel portálu. Obnovit řídicí panel, klikněte na tlačítko **upravit řídicí panel**a pak klikněte pravým tlačítkem myši a vyberte **resetovat do výchozího stavu**.

### <a name="quick-access-to-online-documentation"></a>Rychlý přístup k online dokumentaci

Pro přístup k dokumentace operátora k Azure Stack, použijte nápovědu a podporu ikonu (otazník) v pravém horním rohu portálu správce. Přesuňte ukazatel myši na ikonu a pak vyberte **Nápověda a podpora**.

### <a name="quick-access-to-help-and-support"></a>Rychlý přístup k nápovědě a podpoře

Pokud vyberte ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce a pak vyberte **nová žádost o podporu**, jedna z následujících výsledků:

- Pokud používáte integrovaný systém, tato akce otevře web, kde můžete přímo otevřít lístek podpory s Microsoft podporu služby zákazníkům (CSS). Odkazovat na [získání podpory](azure-stack-manage-basics.md#where-to-get-support) pochopit, kdy byste se měli zúčastnit prostřednictvím podpory Microsoftu nebo prostřednictvím podpory výrobce OEM (OEM) dodavatele hardwaru.
- Pokud používáte development kit, tato akce otevře web fór Azure Stack přímo. Tato fóra jsou pravidelně monitorované. Vývojová sada je prostředí pro testování, neexistuje žádné oficiální podpora dostupná prostřednictvím Microsoft CSS.

### <a name="quick-access-to-the-azure-roadmap"></a>Rychlý přístup k Azure do budoucna

Pokud vyberete **Nápověda a podpora** (otazník) v pravém horním rohu portálu a pak vyberte správce **plány Azure do budoucna**, otevře se nová karta prohlížeče a přejdete do plány Azure do budoucna. Zadáním **Azure Stack** v **produkty** vyhledávacího pole, zobrazí se všechny služby Azure Stack plán aktualizace.

## <a name="next-steps"></a>Další postup

- [Správa oblastí ve službě Azure Stack](azure-stack-region-management.md)
