---
title: MSIX připojení k aplikaci Virtual Desktop v systému Windows – Azure
description: Glosář aplikací MSIX s připojením podmínek a konceptů.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: deb02c161dfe41bf79b7905fde23678425aaf55f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448368"
---
# <a name="msix-app-attach-glossary"></a>Glosář připojení aplikace MSIX

Tento článek je seznam definic klíčových pojmů a konceptů souvisejících s připojením aplikace MSIX.

## <a name="msix-container"></a>Kontejner MSIX

Kontejner MSIX je místo, kde se spouštějí aplikace MSIX. Další informace najdete v tématu [MSIX Containers](/windows/msix/msix-container).

## <a name="msix-application"></a>Aplikace MSIX 

Aplikace uložená v. Soubor MSIX

## <a name="msix-package"></a>Balíček MSIX 

Balíček MSIX je soubor nebo aplikace MSIX.

## <a name="msix-share"></a>MSIX sdílená složka

Sdílená složka MSIX je sdílená síťová složka, která obsahuje rozšířené balíčky MSIX. Sdílené složky MSIX podporují protokol SMB 3 nebo novější. Aplikace se připravené z této sdílené složky MSIX bez nutnosti přesunout soubory aplikace na systémovou jednotku.

## <a name="msix-image"></a>Obrázek MSIX

MSIX Image je soubor VHD, VHDx nebo CIM, který obsahuje jednu nebo více zabalených aplikací MSIX. Každá aplikace je dodávána v imagi MSIX pomocí nástroje MSIXMGR.

## <a name="repackage"></a>Znovu zabalit

Opětovné sbalení přebírá aplikaci, která není MSIX, a převede ji na MSIX pomocí nástroje pro sbalení MSIX (MPT). Další informace najdete v tématu [Přehled nástrojů pro MSIX balení](/windows/msix/packaging-tool/tool-overview).

## <a name="expand-an-msix-package"></a>Rozbalení balíčku MSIX

Rozbalení balíčku MSIX je proces s více kroky. Rozšíření vezme soubor MSIX a vloží jeho obsah do souboru VHD (x) nebo CIM. 

Postup rozšíření balíčku MSIX:

1. Získání balíčku MSIX (soubor MSIX)
2. Přejmenujte soubor MSIX na soubor. zip.
3. Vyextrahování výsledného souboru. zip ve složce.
4. Vytvořte virtuální pevný disk, který má stejnou velikost jako složka.
5. Připojte virtuální pevný disk.
6. Inicializujte disk.
7. Vytvořte oddíl.
8. Naformátujte oddíl.
9. Zkopírujte obsah z komprimovaného souboru do virtuálního pevného disku.
10. Pomocí nástroje MSIXMGR můžete použít seznamy ACL pro obsah VHD.
11. Odpojte virtuální pevný disk (x) nebo [CIM](#cim).

## <a name="upload-an-msix-package"></a>Nahrání balíčku MSIX 

Nahrání balíčku MSIX zahrnuje nahrávání VHD (x) nebo [CIM](#cim) , který obsahuje rozbalený balíček MSIX do sdílené složky MSIX.

Na virtuálním počítači s Windows se nahrávají pro každou sdílenou složku MSIX. Po nahrání balíčku se na něj můžou odkazovat všechny fondy hostitelů ve stejném předplatném.

## <a name="add-an-msix-package"></a>Přidání balíčku MSIX

Na virtuálním počítači s Windows se přidá balíček MSIX, který ho propojí s fondem hostitelů.

## <a name="publish-an-msix-package"></a>Publikování balíčku MSIX 

Na virtuálním počítači s Windows musí být publikovaný balíček MSIX přiřazený uživateli služby Doména služby Active Directory (služba AD DS) nebo skupině uživatelů Azure Active Directory (Azure AD).

## <a name="staging"></a>Příprava

Příprava zahrnuje dvě věci:

- Připojení VHD (x) nebo [CIM](#cim) k virtuálnímu počítači.
- Informování o operačním systému, že je balíček MSIX k dispozici pro registraci.

## <a name="registration"></a>Registrace

Registrace zpřístupňuje připravený balíček MSIX pro vaše uživatele. Registrace je založená na jednotlivých uživatelích. Pokud jste aplikaci pro konkrétního uživatele nezaregistrovali explicitně, nebude možné aplikaci spustit.

Existují dva typy registrace: pravidelné a zpožděné.

### <a name="regular-registration"></a>Pravidelná registrace

Při běžné registraci je každá aplikace přiřazená uživateli plně registrovaná. K registraci dochází v době, kdy se uživatel přihlašuje k relaci, což může ovlivnit dobu potřebnou k tomu, aby mohli začít používat virtuální počítač s Windows.

### <a name="delayed-registration"></a>Opožděná registrace

V případě opožděné registrace jsou jednotlivé aplikace přiřazené uživateli pouze částečně registrovány. Částečná registrace znamená, že jsou zaregistrovaná dlaždice nabídky Start a poklikání na přidružení souborů. K registraci dojde, když se uživatel přihlásí ke své relaci, takže má minimální dopad na dobu potřebnou k zahájení práce s virtuálním počítačem s Windows. Registrace se dokončí jenom v případě, že uživatel spustí aplikaci v balíčku MSIX.

Opožděná registrace je aktuálně výchozí konfigurací aplikace MSIX připojit.

## <a name="deregistration"></a>Zrušení registrace

Při zrušení registrace dojde k odebrání registrovaného, ale nespuštěného balíčku MSIX pro uživatele. K zrušení registrace dojde, když se uživatel odhlásí od své relace. Během Odregistrace aplikace MSIX připojí k místnímu uživatelskému profilu nabízená data, která jsou specifická pro uživatele.

## <a name="destage"></a>Depříprava

Defázování upozorní operační systém, že balíček MSIX nebo aplikace, které aktuálně nejsou spuštěné a které nejsou připravené pro žádného uživatele, se dají odpojit. Tím se odebere všechny odkazy na tento operační systém.

## <a name="cim"></a>CIM

. CIM je nová přípona souboru přidružená k systému souborů kompozitních imagí (CimFS). Připojení a odpojení souborů CIM je rychlejší pro soubory VHD. CIM také spotřebovává méně procesoru a paměti než virtuální pevný disk.

Soubor CIM je soubor s příponou. Rozšíření CIM obsahující metadata a alespoň šest dalších souborů, které obsahují skutečná data. Soubory v souboru CIM nemají rozšíření. V následující tabulce je seznam ukázkových souborů, které byste našli v modelu CIM:

| Název souboru | Linka | Velikost |
|-----------|-----------|------|
| VIRTUÁLNÍ čipové karty | CIM | 1 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264 132 KB |

Následující tabulka představuje porovnání výkonu mezi VHD a CimFS. Tato čísla byla výsledkem testovacího běhu s 500 300 MB souborů v každém formátu spuštěném na DSv4 počítači.

|  Specifikace                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Průměrná doba připojení     | 356 MS                     | 255 MS      |
| Průměrná doba odpojení   | 1615 MS                    | 36 MS       |
| Memory consumption | 6% (z 8 GB)                      | 2% (z 8 GB)       |
| PROCESOR (počet Špičk)          | Vyčerpáním víckrát | Žádný dopad |

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o připojení aplikace MSIX, podívejte se na náš [Přehled](what-is-app-attach.md) a [Nejčastější dotazy](app-attach-faq.md). V opačném případě začněte s [nastavením připojit k aplikaci](app-attach.md).
