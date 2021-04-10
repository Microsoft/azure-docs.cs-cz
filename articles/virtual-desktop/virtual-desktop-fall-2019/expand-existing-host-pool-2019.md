---
title: Rozbalte existující fond hostitelů Windows Virtual Desktop (Classic) s novými hostiteli relací – Azure
description: Postup rozšíření stávajícího fondu hostitelů na nové hostitele relací ve virtuální ploše Windows (Classic).
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f82b831d887f0ebdd659167935f2134583b3bb87
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551947"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Rozšíření existujícího fondu hostitelů na nové hostitele relací ve virtuálním počítači s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../expand-existing-host-pool.md).

Při vyčerpání využití v rámci fondu hostitelů možná budete muset rozšířit svůj stávající fond hostitelů o nové hostitele relací, aby bylo možné nové zatížení zpracovat.

V tomto článku se dozvíte, jak můžete rozšířit existující fond hostitelů o nové hostitele relací.

## <a name="what-you-need-to-expand-the-host-pool"></a>Co potřebujete k rozšíření fondu hostitelů

Než začnete, ujistěte se, že jste vytvořili fond hostitelů a virtuální počítače hostitele relace (VM) pomocí jedné z následujících metod:

- [Nabídka Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Šablona Azure Resource Manager GitHubu](create-host-pools-arm-template.md)
- [Vytvoření fondu hostitelů pomocí PowerShellu](create-host-pools-powershell-2019.md)

Při prvním vytvoření fondu hostitelů a hostitele relací budete také potřebovat následující informace:

- Velikost virtuálního počítače, obrázek a předpona názvu
- Pověření pro připojení k doméně a správce tenanta virtuálních počítačů s Windows
- Název virtuální sítě a název podsítě

Další tři části představují tři metody, které můžete použít k rozšíření fondu hostitelů. Můžete to udělat s libovolným nástrojem pro nasazení, se kterým máte v pohodlí.

>[!NOTE]
>Během fáze nasazení uvidíte chybové zprávy pro předchozí prostředky virtuálních počítačů hostitele relace, pokud jsou aktuálně vypnuté. K těmto chybám dochází, protože Azure nemůže spustit rozšíření PowerShell DSC, aby ověřil, jestli jsou virtuální počítače hostitele relace správně zaregistrované ve vašem existujícím fondu hostitelů. Hostitel relace, jehož název končí na "-0", musí být spuštěný, ale tyto chyby můžete bezpečně ignorovat pro ostatní hostitele relací nebo se můžete vyhnout chybám spuštěním všech virtuálních počítačů hostitele relace v existujícím fondu hostitelů před zahájením procesu nasazení.

## <a name="redeploy-from-azure"></a>Opětovné nasazení z Azure

Pokud jste už vytvořili fond hostitelů a hostitele relací pomocí Azure Resource Manager šablony [Azure Marketplace nabídky](create-host-pools-azure-marketplace-2019.md) nebo [GitHubu](create-host-pools-arm-template.md), můžete stejnou šablonu znovu nasadit z Azure Portal. Opětovné nasazení šablony automaticky znovu zadá všechny informace, které jste zadali do původní šablony s výjimkou hesel.

Zde je postup, jak znovu nasadit šablonu Azure Resource Manager pro rozšíření fondu hostitelů:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Na panelu hledání v horní části Azure Portal vyhledejte **skupiny prostředků** a vyberte položku v části **služby**.
3. Vyhledejte a vyberte skupinu prostředků, kterou jste vytvořili při vytváření fondu hostitelů.
4. V panelu na levé straně prohlížeče vyberte **nasazení**.
5. Vyberte příslušné nasazení pro proces vytváření fondu hostitelů:
     - Pokud jste vytvořili původní fond hostitelů pomocí nabídky Azure Marketplace, vyberte nasazení od služby **RDS. WVD-provision-Host-Pool**.
     - Pokud jste vytvořili původní fond hostitelů pomocí šablony Azure Resource Manager GitHubu, vyberte nasazení s názvem **Microsoft. template**.
6. Vyberte **znovu nasadit**.

     >[!NOTE]
     >Pokud se šablona po výběru **opětovného nasazení** automaticky znovu nespustí, vyberte **šablonu** na panelu na levé straně prohlížeče a pak vyberte **nasadit**.

7. Vyberte skupinu prostředků, která obsahuje virtuální počítače hostitele aktuální relace v existujícím fondu hostitelů.

     >[!NOTE]
     >Pokud se zobrazí chyba s informací, že chcete vybrat jinou skupinu prostředků, i když je ta, kterou jste zadali, je správná, vyberte jinou skupinu prostředků a pak vyberte původní skupinu prostředků.

8. Jako *_artifactsLocation* zadejte následující adresu URL: `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Zadejte nový celkový počet hostitelů relací, které chcete mít, do *počtu instancí relace vzdálené plochy*. Pokud například rozšíříte fond hostitelů z pěti hostitelů relace na osm, zadejte **8**.
10. Zadejte stejné existující heslo k doméně, které jste použili pro existující hlavní název uživatele domény. Neměňte uživatelské jméno, protože při spuštění šablony dojde k chybě.
11. Zadejte stejné heslo správce klienta, které jste použili pro ID uživatele nebo aplikace, které jste zadali pro *hlavní název uživatele (UPN) správce klienta nebo ID aplikace*. Znovu neměňte uživatelské jméno.
12. Dokončete odeslání pro rozšíření fondu hostitelů.

## <a name="run-the-azure-marketplace-offering"></a>Spuštění nabídky Azure Marketplace

Postupujte podle pokynů v části [Vytvoření fondu hostitelů pomocí Azure Marketplace,](create-host-pools-azure-marketplace-2019.md) dokud nedosáhnete [spuštění nabídky Azure Marketplace ke zřízení nového fondu hostitelů](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool). Až se dostanete k tomuto okamžiku, budete muset pro každou kartu zadat následující informace:

### <a name="basics"></a>Základy

Všechny hodnoty v této části by se měly shodovat s tím, co jste zadali při prvním vytváření virtuálních počítačů hostitelů a hostitele relací, s výjimkou *výchozích uživatelů plochy*:

1.    V poli *předplatné* vyberte předplatné, ve kterém jste poprvé vytvořili fond hostitelů.
2.    V poli *Skupina prostředků* vyberte stejnou skupinu prostředků, ve které se nacházejí existující virtuální počítače hostitele relace fondu hostitelů.
3.    V poli *oblast* vyberte stejnou oblast, ve které se nacházejí existující virtuální počítače hostitele relace fondu hostitelů.
4.    Do pole *název Hostpool* zadejte název existujícího fondu hostitelů.
5.    Jako *typ stolního počítače* vyberte typ pracovní plochy, který odpovídá stávajícímu fondu hostitelů.
6.    U *výchozích uživatelů plochy* zadejte seznam oddělený čárkami všech dalších uživatelů, kteří se chtějí přihlašovat k klientům virtuálních klientů Windows, a získat přístup k ploše po dokončení nabídky Azure Marketplace. Například pokud chcete přiřadit user3@contoso.com a user4@contoso.com přístup, zadejte user3@contoso.com , user4@contoso.com .
7.    Vyberte **Další: konfigurace virtuálního počítače**.

>[!NOTE]
>S výjimkou *výchozích uživatelů stolních počítačů* se všechna pole musí přesně shodovat s tím, co bylo konfigurováno v existujícím fondu hostitelů. Pokud dojde k neshodě, která bude mít za následek nový fond hostitelů.

### <a name="configure-virtual-machines"></a>Konfigurace virtuálních počítačů

Všechny hodnoty parametrů v této části se musí shodovat s tím, co jste zadali při prvním vytváření virtuálních počítačů hostitelů a hostitele relací, s výjimkou celkového počtu virtuálních počítačů. Počet virtuálních počítačů, které zadáte, bude počet virtuálních počítačů ve fondu rozbalených hostitelů:

1. Vyberte velikost virtuálního počítače, která odpovídá stávajícím virtuálním počítačům hostitele relace.

    >[!NOTE]
    >Pokud se konkrétní velikost virtuálního počítače, kterou hledáte, nezobrazuje v selektoru velikosti virtuálního počítače, je to proto, že jsme ho ještě nepřipojili k nástroji Azure Marketplace.

2. Pokud chcete vybrat celkový počet hostitelů relací, které chcete mít ve fondu hostitelů, upravte *profil využití*, *Celkový počet uživatelů* a *počet parametrů virtuálních počítačů* . Pokud například rozšíříte fond hostitelů z pěti hostitelů relace na osm, nakonfigurujte tyto možnosti tak, aby se dosáhlo 8 virtuálních počítačů.
3. Zadejte předponu názvů virtuálních počítačů. Pokud například zadáte název "prefix", virtuální počítače budou nazývány "prefix-0," prefix-1, "atd.
4. Vyberte **Další: nastavení virtuálního počítače**.

### <a name="virtual-machine-settings"></a>Nastavení virtuálního počítače

Všechny hodnoty parametrů v této části se musí shodovat s tím, co jste zadali při prvním vytvoření fondu hostitelů a hostitelů relací hostitele:

1. V části *zdroj image* a *image operačního systému* zadejte stejné informace, jaké jste zadali při prvním vytvoření fondu hostitelů.
2. V případě *hlavního názvu uživatele (UPN) pro připojení k doméně AD* a přidružených hesel zadejte stejné informace, jaké jste zadali při prvním vytvoření fondu hostitelů pro připojení k virtuálním počítačům do domény služby Active Directory. Tyto přihlašovací údaje se použijí k vytvoření místního účtu na virtuálních počítačích. Můžete resetovat tyto místní účty a později změnit jejich přihlašovací údaje.
3. Pro informace o virtuální síti vyberte stejnou virtuální síť a podsíť, pro které se nacházejí existující virtuální počítače hostitele relace fondu hostitelů.
4. Vyberte **Další: Konfigurace informací o virtuálním počítači s Windows**.

### <a name="windows-virtual-desktop-information"></a>Informace o virtuálním počítači s Windows

Všechny hodnoty parametrů v této části se musí shodovat s tím, co jste zadali při prvním vytvoření fondu hostitelů a hostitelů relací hostitele:

1. V poli *název skupiny tenantů virtuálních klientů Windows* zadejte název skupiny tenantů, která obsahuje vašeho tenanta. Pokud jste nezadali konkrétní název skupiny tenantů, ponechte ho jako výchozí.
2. Do pole *název tenanta virtuálních klientů Windows* zadejte název tenanta, do kterého budete tento fond hostitelů vytvářet.
3. Zadejte stejné přihlašovací údaje, které jste použili při prvním vytvoření fondu hostitelů a virtuálních počítačů hostitele relací. Pokud používáte instanční objekt, zadejte ID instance Azure Active Directory, kde se nachází váš instanční objekt.
4. Vyberte **Další: zkontrolovat + vytvořit**.

## <a name="run-the-github-azure-resource-manager-template"></a>Spuštění šablony Azure Resource Manager GitHubu

Postupujte podle pokynů v tématu [spuštění šablony Azure Resource Manager pro zřízení nového fondu hostitelů](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) a zadání všech stejných hodnot parametrů s výjimkou *počtu instancí vzdálené relace hostitele*. Po spuštění šablony zadejte počet virtuálních počítačů hostitelů relací, které chcete ve fondu hostitelů. Pokud například rozšíříte fond hostitelů z pěti hostitelů relace na osm, zadejte **8**.

## <a name="next-steps"></a>Další kroky

Teď, když jste rozšířili stávající fond hostitelů, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho jako součást uživatelské relace. K relaci se můžete připojit pomocí kteréhokoli z následujících klientů:

- [Připojení s desktopovým klientem Windows](connect-windows-7-10-2019.md)
- [Připojení k webovému klientovi](connect-web-2019.md)
- [Práce s klientem Android](connect-android-2019.md)
- [Připojení s klientem macOS](connect-macos-2019.md)
- [Připojení s klientem iOS](connect-ios-2019.md)
