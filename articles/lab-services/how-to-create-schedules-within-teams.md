---
title: Vytváření plánů Azure Lab Services v rámci týmů
description: Naučte se vytvářet plány testovacích služeb v rámci týmů.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 5578a2f63a4d6f58998a54560299a4356ed65997
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946670"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Vytváření a Správa plánů testovacích služeb v rámci týmů

Plány umožňují nakonfigurovat prostředí učebny tak, aby se virtuální počítače v testovacím prostředí automaticky spouštěly a vypnuly v určitou dobu. Můžete definovat Jednorázový plán nebo plán opakování. Následující postupy vám poskytnou kroky pro vytváření a správu plánů pro prostředí učebny: 

Tady je postup, jaký vliv mají plány na virtuální počítače testovacího prostředí: 

- Virtuální počítač šablony není zahrnutý v plánech. 
- Spuštěny jsou pouze přiřazené virtuální počítače. To znamená, že pokud počítač není vyžádán koncovým uživatelem (student), počítač se v naplánovaných hodinách nespustí. 
- Všechny virtuální počítače (bez ohledu na to, jestli je uživatel vyžádal), se zastaví v závislosti na plánu testovacího prostředí. 

> [!IMPORTANT]
> Plánovaná doba spuštění virtuálních počítačů se nepočítá s kvótou přidělenou uživateli. Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích. 

## <a name="createeditdelete-a-schedule-for-the-lab"></a>Vytvořit, upravit nebo odstranit plán testovacího prostředí

Vytvořte naplánovanou událost pro testovací prostředí, aby se virtuální počítače v testovacím prostředí automaticky spustily nebo zastavily v určitých časech. Kvóta uživatele, kterou jste zadali dříve, je další čas přiřazený každému uživateli mimo tento naplánovaný čas. 

1. Přepněte na stránku **plány** a na panelu nástrojů (nahoře nebo nalevo od okna) vyberte **Přidat naplánovanou událost** . 
1. Pro plán nastavte následující parametry:
    1. Potvrďte, že **Standard** je vybraný jako **Typ události**. Vyberte **pouze spustit** , chcete-li zadat pouze počáteční čas pro virtuální počítače. Vyberte možnost **zastavit pouze** a zadejte pouze dobu zastavení virtuálních počítačů. 
    1. Zadejte **počáteční datum**.
    1. Zadejte **čas spuštění** , ve kterém chcete virtuální počítače spustit.
    1. Zadejte **čas ukončení** , ve kterém mají být virtuální počítače vypnuté. 
    1. Zadejte **časové pásmo** pro dobu zahájení a ukončení, kterou jste zadali. 
    1. V části **Opakovat** vyberte **každý týden** nebo **nikdy**. 
    1. Pro **poznámky (volitelné)** zadejte libovolný popis nebo poznámky k plánu. 
1. Klikněte na **Uložit**. 

### <a name="view-schedules-in-calendar"></a>Zobrazit plány v kalendáři

V kalendáři můžete zobrazit naplánovaná data a časy zvýrazněné. V pravém horním rohu vyberte tlačítko **dnes** a přepněte tak na aktuální datum v kalendáři. Vyberte **šipku doleva** pro přepnutí na předchozí týden a **šipku vpravo** , abyste přešli na další týden v kalendáři. 

### <a name="edit-a-schedule"></a>Upravit plán

Když v kalendáři vyberete zvýrazněný plán, zobrazí se tlačítka pro **úpravu** nebo **odstranění** plánu. 

Na stránce **Upravit naplánovanou událost** můžete plán aktualizovat a vybrat **Uložit**. 

### <a name="delete-a-schedule"></a>Odstranění plánu

1. Chcete-li odstranit plán, vyberte zvýrazněný plán v kalendáři a vyberte ikonu odpadkového koše (odstranit):
1. Kliknutím na **tlačítko Ano** v dialogovém okně **odstranit naplánovanou událost** potvrďte odstranění. 

## <a name="automatic-shutdown-and-disconnect-settings"></a>Nastavení automatického vypnutí a odpojení

V horní části stránky se zobrazí odkaz na nastavení automatické **vypnutí** .

Můžete povolit několik funkcí řízení nákladů na automatické vypnutí, abyste proaktivně zabránili dalším nákladům, když se virtuální počítače aktivně nepoužívají. Kombinace následujících tří funkcí automatického vypnutí a odpojení zachytává většinu případů, kdy uživatelé omylem odejdou z virtuálních počítačů, na kterých běží:
 
- Automaticky odpojí uživatele z virtuálních počítačů, které operační systém považuje za nečinné.
- Automaticky vypne virtuální počítače, když se uživatelé odpojí.
- Automaticky vypne virtuální počítače, které jsou spuštěné, ale uživatelé se nepřipojí.

Pokud chcete získat další informace, klikněte na *informační* ikonu vedle možnosti v nastavení.

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Přehled použití Azure Lab Services v rámci týmů](lab-services-within-teams-overview.md)
- [Začínáme a vytvoření testovacího prostředí testovacích služeb z týmů](how-to-get-started-create-lab-within-teams.md)
- [Správa seznamů uživatelů testovacích služeb z týmů](how-to-manage-user-lists-within-teams.md)
- [Správa fondu virtuálních počítačů ve službě Lab Services z týmů](how-to-manage-vm-pool-within-teams.md)
- [Přístup k virtuálnímu počítači (zobrazení student) ve službě Lab Services z týmů](how-to-access-vm-for-students-within-teams.md)