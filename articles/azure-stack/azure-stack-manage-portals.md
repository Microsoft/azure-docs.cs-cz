---
title: Pomocí portálu pro správu ve službě Azure Stack | Dokumentace Microsoftu
description: Jako operátory Azure stacku Další informace o použití portálu pro správu.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/07/2019
ms.author: jeffgilb
ms.reviewer: ''
ms.lastreviewed: 01/07/2019
ms.openlocfilehash: bda4edeb610fd786307a751afc5b397a2f597e54
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767465"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Rychlý start: použití portálu pro správu služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Existují dva portály ve službě Azure Stack; na portálu pro správu a na portálu user portal (někdy označovány jako *tenanta* portálu.) Jako operátor Azure stacku můžete použít portál pro správu pro každodenní správu a operace služby Azure Stack.

## <a name="access-the-administrator-portal"></a>Přístup k portálu správce

Pro přístup k portálu správce, přejděte na adresu URL portálu a přihlášení pomocí přihlašovacích údajů operátory Azure stacku. Pro integrovaný systém portálu, k němuž adresy URL se liší podle oblasti názvu a externí plně kvalifikovaný název domény (FQDN) vašeho nasazení Azure Stack. Na portálu pro správu adresy URL je vždy stejný pro nasazení Azure Stack Development Kit (ASDK). 

| Prostředí | Adresa URL portálu pro správce |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Integrované systémy | https://adminportal.&lt;*region*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> ASDK prostředí, budete muset nejprve se ujistěte, že můžete [připojení k hostiteli development kit](azure-stack-connect-azure-stack.md) prostřednictvím připojení ke vzdálené ploše nebo virtuální privátní sítě (VPN).

 ![Na portálu pro správu](media/azure-stack-manage-portals/admin-portal.png)

Výchozí časové pásmo pro všechna nasazení Azure Stack nastavený na koordinovaný univerzální čas (UTC). 

Na portálu správce můžete provádět věci, jako je:

* [Registrace Azure Stack s Azure](azure-stack-registration.md)
* [Naplnění webu marketplace](azure-stack-download-azure-marketplace-item.md)
* [Vytvořit plány, nabídky a předplatné pro uživatele](azure-stack-plan-offer-quota-overview.md)
* [Monitorování stavu a upozornění](azure-stack-monitor-health.md)
* [Správa aktualizací Azure Stack](azure-stack-updates.md)

**Rychlý úvodní kurz** dlaždice obsahuje odkazy na online dokumentaci pro nejběžnější úkoly.

I když operátor mohou vytvořit prostředky, jako jsou virtuální počítače, virtuální sítě a účty úložiště v portálu pro správu, měli byste [přihlásit k portálu user portal](user/azure-stack-use-portal.md) vytvořit a otestovat prostředky.

>[!NOTE]
>**Vytvoření virtuálního počítače** má odkaz na dlaždici kurz rychlý start můžete vytvořit virtuální počítač v portálu pro správu, ale to je určené jenom k ověření úspěšného nasazení Azure Stack.

## <a name="understand-subscription-behavior"></a>Pochopte chování předplatného

Existují tři předplatná vytvořená ve výchozím nastavení v portálu správy. využití, výchozího zprostředkovatele a měření. Jakožto Obsluha budete nejčastěji používat *výchozí předplatné poskytovatele*. Nelze přidat další předplatná a jejich použití v portálu pro správu. 

Další předplatná jsou vytvořené uživateli na portálu user portal na základě plánů a nabídek, které vytvoříte pro ně. Portál user portal, ale neposkytuje přístup k libovolnému administrativních nebo provozních možnosti na portálu pro správu.

Portály pro správu a uživatel se zálohují na samostatných instancí služby Azure Resource Manageru. Z důvodu toto oddělení Azure Resource Manageru předplatná nepřecházejí portálů. Například pokud, jako operátor Azure stacku, přihlášení k portálu user portal, nelze získat přístup *výchozí předplatné poskytovatele*. I když nemáte přístup pro všechny funkce správy, můžete vytvořit předplatná sami z veřejné dostupným nabídkám. Tak dlouho, dokud jste přihlášení k portálu user portal se považují za tenanta uživatele.

  >[!NOTE]
  >V prostředí ASDK Pokud uživatel patří do stejného adresáře tenanta jako operátor Azure stacku mu nejsou zablokovat přihlášení k portálu pro správu. Však nelze použít žádnou z funkcí správy nebo přidání předplatného pro přístup k nabídky, která jsou k dispozici na portálu user portal.

## <a name="administration-portal-tips"></a>Tipy portálu pro správu

### <a name="customize-the-dashboard"></a>Přizpůsobení řídicího panelu

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete vybrat **upravit řídicí panel** změnit výchozí řídicí panel, nebo vyberte **nový řídicí panel** přidáte vlastní řídicí panel. Dlaždice můžete snadno přidat na řídicí panel. Například můžete vybrat **+ vytvořit prostředek**, klikněte pravým tlačítkem na **nabízí + plány**a pak vyberte **připnout na řídicí panel**.

V některých případech se může zobrazit prázdný řídicí panel portálu. Obnovit řídicí panel, klikněte na tlačítko **upravit řídicí panel**a pak klikněte pravým tlačítkem a vyberte **resetovat do výchozího stavu**.

### <a name="quick-access-to-online-documentation"></a>Rychlý přístup k online dokumentaci

Pro přístup k dokumentaci k Azure Stack operátor, v nápovědě a podpoře ikonu (otazník) v pravém horním rohu portálu správce. Přesuňte ukazatel myši na ikonu a pak vyberte **Nápověda a podpora**.

### <a name="quick-access-to-help-and-support"></a>Rychlý přístup k nápovědě a podpoře

Pokud vyberte ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce a pak vyberte **nová žádost o podporu**, jeden z následujících výsledků se stane:

- Pokud používáte integrovaný systém, tato akce otevře web, kde můžete přímo otevřít lístek podpory s Microsoft podporu služby zákazníkům (CSS). Odkazovat na [získání podpory](azure-stack-manage-basics.md#where-to-get-support) pochopit, kdy byste se měli zúčastnit prostřednictvím podpory Microsoftu nebo prostřednictvím podpory výrobce OEM (OEM) dodavatele hardwaru.
- Pokud používáte ASDK, tato akce otevře [webu fór Azure Stack](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) přímo. Tato fóra jsou pravidelně monitorované. Vzhledem k tomu, ASDK zkušební prostředí, neexistuje žádné oficiální podpora dostupná prostřednictvím Microsoft CSS.

### <a name="quick-access-to-the-azure-roadmap"></a>Rychlý přístup k Azure do budoucna

Pokud vyberete **Nápověda a podpora** (otazník) v pravém horním rohu portálu a potom vyberte možnost správy **plány Azure do budoucna**, otevře se nová karta prohlížeče a přejdete do plány Azure do budoucna. Zadáním **Azure Stack** v **produkty** vyhledávacího pole, zobrazí se všechny služby Azure Stack plán aktualizace.

## <a name="next-steps"></a>Další postup

[Registrace Azure Stack s využitím Azure](azure-stack-registration.md) a naplňte jimi [marketplace služby Azure Stack](azure-stack-marketplace.md) s položkami a nabídnout uživatelům. 
