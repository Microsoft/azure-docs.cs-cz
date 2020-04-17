---
title: Vytvoření mezipaměti Azure HPC
description: Jak vytvořit instanci mezipaměti Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: befbe2435a518b82cf5a3ab12e6129aa3ce5c22b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537966"
---
# <a name="create-an-azure-hpc-cache"></a>Vytvoření mezipaměti Azure HPC

K vytvoření mezipaměti použijte portál Azure.

![snímek obrazovky s přehledem mezipaměti na Webu Azure Portal s tlačítkem Vytvořit dole](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definování základních podrobností

![snímek obrazovky se stránkou s podrobnostmi o projektu na webu Azure Portal](media/hpc-cache-create-basics.png)

V **části Podrobnosti projektu**vyberte předplatný a skupinu prostředků, která bude hostovat mezipaměť.

V **části Podrobnosti o službě**nastavte název mezipaměti a tyto další atributy:

* Umístění – vyberte jednu z [podporovaných oblastí](hpc-cache-overview.md#region-availability).
* Virtuální síť – můžete vybrat existující nebo vytvořit novou virtuální síť.
* Podsíť – zvolte nebo vytvořte podsíť s alespoň 64 IP adresami (/24), která se použije jenom pro tuto instanci mezipaměti Azure HPC.

## <a name="set-cache-capacity"></a>Nastavení kapacity mezipaměti
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stránce **Mezipaměť** je nutné nastavit kapacitu mezipaměti. Zde nastavené hodnoty určují, kolik dat může vaše mezipaměť uchovat a jak rychle může obsluhovat požadavky klientů.

Kapacita také ovlivňuje náklady mezipaměti.

Zvolte kapacitu nastavením těchto dvou hodnot:

* Maximální rychlost přenosu dat pro mezipaměť (propustnost) v GB/s
* Velikost úložiště přiděleného pro data uložená v mezipaměti v TB

Zvolte jednu z dostupných hodnot propustnosti a velikosti úložiště mezipaměti.

Mějte na paměti, že skutečná rychlost přenosu dat závisí na zatížení, rychlosti sítě a typu cílů úložiště. Hodnoty, které zvolíte, nastaví maximální propustnost pro celý systém mezipaměti, ale některé z těchto hodnot se používají pro režijní úlohy. Pokud například klient požaduje soubor, který ještě není uložený v mezipaměti, nebo pokud je soubor označen jako zastaralý, vaše mezipaměť používá část své propustnosti k jeho načtení z back-endového úložiště.

Azure HPC Cache spravuje, které soubory jsou uloženy do mezipaměti a předinstalovány, aby se maximalizovaly míry přístupů do mezipaměti. Obsah mezipaměti je průběžně vyhodnocován a soubory jsou přesunuty do dlouhodobého úložiště, pokud jsou méně často přístupné. Zvolte velikost úložiště mezipaměti, která může pohodlně pojmout aktivní sadu pracovních souborů s dalším prostorem pro metadata a další režii.

![snímek obrazovky stránky pro změna velikosti mezipaměti](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Povolení šifrování Azure Key Vault (volitelné)

Pokud se vaše mezipaměť nachází v oblasti, která podporuje šifrovací klíče spravované zákazníkem, zobrazí se stránka **Klíče šifrování disku** mezi kartami **Mezipaměť** a **Značky.** Od doby zveřejnění je tato možnost podporována ve východních USA, středních usa a 2 .

Pokud chcete spravovat šifrovací klíče používané s úložištěm mezipaměti, zadejte informace o azure key vaultu na stránce **šifrovací klíče disku.** Trezor klíčů musí být ve stejné oblasti a ve stejném předplatném jako mezipaměť.

Tuto část můžete přeskočit, pokud nepotřebujete klíče spravované zákazníkem. Azure ve výchozím nastavení šifruje data pomocí klíčů spravovaných microsoftem. Další informace najdete v [šifrování úložiště Azure.](../storage/common/storage-service-encryption.md)

> [!NOTE]
>
> * Po vytvoření mezipaměti nelze přepínat mezi klíči spravovanými společností Microsoft a klíči spravovanými zákazníky.
> * Po vytvoření mezipaměti je nutné ji autorizovat pro přístup k trezoru klíčů. Chcete-li zapnout šifrování, klepněte na tlačítko **Povolit šifrování** na stránce **Přehled** mezipaměti. Tento krok udělejte do 90 minut od vytvoření mezipaměti.
> * Disky mezipaměti jsou vytvořeny po této autorizaci. To znamená, že počáteční doba vytvoření mezipaměti je krátká, ale mezipaměť nebude připravena k použití po dobu deseti minut nebo déle po autorizaci přístupu.

Úplné vysvětlení procesu šifrování klíče spravovaného zákazníkem načtete [použití šifrovacích klíčů spravovaných zákazníkem pro azure hpc cache](customer-keys.md).

![snímek obrazovky stránky šifrovacích klíčů s vybranými poli "zákazníkem spravovanými" a klíčovými poli úschovny](media/create-encryption.png)

Vyberte **Zákazník se podařilo** zvolit šifrování klíče spravované zákazníkem. Zobrazí se pole specifikace trezoru klíčů. Vyberte trezor klíčů Azure, který chcete použít, a pak vyberte klíč a verzi, která se má použít pro tuto mezipaměť. Klíč musí být 2048bitový klíč RSA. Na této stránce můžete vytvořit novou trezor klíčů, klíč nebo verzi klíče.

Po vytvoření mezipaměti je nutné ji autorizovat k použití služby trezoru klíčů. Podrobnosti [načlánekpočte autorizovat šifrování azure trezoru klíčů z mezipaměti.](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)

## <a name="add-resource-tags-optional"></a>Přidání značek prostředků (volitelné)

Stránka **Značky** umožňuje přidat [značky prostředků](https://go.microsoft.com/fwlink/?linkid=873112) do instance mezipaměti Azure HPC.

## <a name="finish-creating-the-cache"></a>Dokončení vytváření mezipaměti

Po konfiguraci nové mezipaměti klikněte na kartu **Revize + vytvoření.** Portál ověřuje vaše výběry a umožňuje zkontrolovat vaše volby. Pokud je vše v pořádku, klepněte na tlačítko **Vytvořit**.

Vytvoření mezipaměti trvá přibližně 10 minut. Průběh můžete sledovat na panelu oznámení na portálu Azure.

![snímek obrazovky se stránkami vytváření mezipaměti "nasazení probíhá" a "oznámení" na portálu](media/hpc-cache-deploy-status.png)

Po dokončení vytváření se zobrazí oznámení s odkazem na novou instanci mezipaměti Azure HPC a mezipaměť se zobrazí v seznamu **prostředků** vašeho předplatného.

![snímek obrazovky s instancí Azure HPC Cache na webu Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Pokud vaše mezipaměť používá šifrovací klíče spravované zákazníkem, může se před dokončením změny stavu nasazení zobrazit v seznamu prostředků. Jakmile je stav mezipaměti **Čekání na klíč,** můžete [jej autorizovat](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) k použití trezoru klíčů.

## <a name="next-steps"></a>Další kroky

Po zobrazení mezipaměti v seznamu **Zdroje** můžete přejít k dalšímu kroku.

* [Definujte cíle úložiště,](hpc-cache-add-storage.md) které budou mít přístup k vašim zdrojům dat v mezipaměti.
* Pokud používáte šifrovací klíče spravované zákazníkem, musíte k dokončení nastavení mezipaměti [autorizovat šifrování azure key vaultu](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) ze stránky s přehledem mezipaměti. Před přidáním úložiště je nutné provést tento krok. Přečtěte si pro podrobnosti [článek Použití šifrovacích klíčů spravovaných zákazníkem.](customer-keys.md)
