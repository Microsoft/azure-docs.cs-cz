---
title: Konfigurace nastavení diagnostiky úložiště ve velkém měřítku
description: Konfigurace nastavení diagnostiky analýzy protokolů pro všechny trezory v daném oboru pomocí zásad Azure
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584502"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Konfigurace nastavení diagnostiky úložiště ve velkém měřítku

Řešení pro vytváření sestav poskytované službou Azure Backup využívá analýzu protokolů (LA). Pro data daného trezoru, která mají být odeslána do LA, je třeba pro tento trezor vytvořit [diagnostické nastavení.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

Ruční přidání nastavení diagnostiky na úschovnu může být často těžkopádný úkol. Kromě toho musí mít každý nový vytvořený trezor také povolené nastavení diagnostiky, aby bylo možné zobrazit sestavy pro tento trezor. 

Pro zjednodušení vytváření nastavení diagnostiky ve velkém měřítku (s LA jako cíl), Azure Backup poskytuje integrované [zásady Azure](https://docs.microsoft.com/azure/governance/policy/). Tato zásada přidá nastavení diagnostiky LA do všech trezorů v daném předplatném nebo skupině prostředků. Následující části obsahují pokyny, jak používat tyto zásady.

## <a name="supported-scenarios"></a>Podporované scénáře

* Zásady lze použít najednou na všechny trezory služby Recovery Services v určitém předplatném (nebo na skupinu prostředků v rámci předplatného). Uživatel přiřazující zásady musí mít přístup vlastníka k předplatnému, ke kterému je zásada přiřazena.

* Pracovní prostor LA určený uživatelem (do kterého budou data diagnostiky odeslána) může být v jiném předplatném než trezory, ke kterým je zásada přiřazena. Uživatel musí mít přístup ke předplatnému, ve kterém existuje zadaný pracovní prostor LA, "Čtenář", Přispěvatel nebo Vlastník.

* Obor skupiny pro správu není aktuálně podporován.

* Vestavěná politika není v současné době k dispozici v národních cloudech.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Přiřazení předdefinované zásady k oboru

Chcete-li přiřadit zásady pro trezory v požadovaném oboru, postupujte podle následujících kroků:

1. Přihlaste se na portál Azure a přejděte na řídicí panel **zásad.**
2. V yberte **Definice** v levé nabídce, chcete-li získat seznam všech předdefinovaných zásad napříč prostředky Azure.
3. Filtrujte seznam pro **Category=Monitoring**. Vyhledejte zásadu s názvem **[Náhled]: Nasazení diagnostických nastavení úložiště služby Recovery Services do pracovního prostoru Log Analytics pro kategorie specifické pro daný prostředek**.

![Okno definice zásad](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Klikněte na název zásady. Budete přesměrováni na podrobnou definici těchto zásad.

![Podrobná definice zásad](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Klikněte na tlačítko **Přiřadit** v horní části čepele. Tím se přesměrujete na okno **Přiřadit zásady.**

6. V části **Základy**klikněte na tři tečky vedle pole **Obor.** Tím se otevře pravé okno kontextu, kde můžete vybrat předplatné pro zásady, které mají být použity na. Volitelně můžete také vybrat skupinu prostředků, aby byla zásada použita pouze pro trezory v určité skupině prostředků.

![Základy přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Do části **Parametry**zadejte tyto informace:

* **Název profilu** - Název, který bude přiřazen k nastavení diagnostiky vytvořeného zásadou.
* **Pracovní prostor analýzy protokolů** – pracovní prostor analýzy protokolů, ke kterému by mělo být přidruženo nastavení diagnostiky. Diagnostická data všech trezorů v rozsahu přiřazení zásad budou zasunuta do zadaného pracovního prostoru LA.

* **Název značky vyloučení (nepovinné) a Hodnota značky vyloučení (volitelné)** – Můžete vyloučit z přiřazení zásad y trezory obsahující určitý název značky a hodnotu. Pokud například **nechcete,** aby bylo do trezorů, které mají značku isTest nastavenou na hodnotu yes, přidáno diagnostické nastavení, musíte do pole **Název značky vyloučení** zadat hodnotu "isTest" a hodnotu ano do pole **Hodnota značky vyloučení.** Pokud některá (nebo obě) z těchto dvou polí zůstanou prázdná, bude zásada použita pro všechny příslušné trezory bez ohledu na značky, které obsahují.

![Parametry přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Vytvoření úkolu nápravy** – Jakmile je zásada přiřazena k oboru, všechny nové úschovny vytvořené v tomto oboru automaticky nakonfigurují nastavení diagnostiky LA (do 30 minut od vytvoření trezoru). Chcete-li přidat nastavení diagnostiky do existujících trezorů v oboru, můžete spustit nápravnou úlohu v době přiřazení zásad. Chcete-li spustit nápravnou úlohu, zaškrtněte políčko **Vytvořit úlohu nápravy**. 

![Náprava přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Přejděte na kartu **Revize+Vytvořit** a klepněte na **tlačítko Vytvořit**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Za jakých podmínek se nápravná úloha bude vztahovat na úschovnu?

Nápravná úloha je použita pro trezory, které nejsou kompatibilní podle definice zásady. Úschovna je nevyhovující, pokud splňuje některou z následujících podmínek:

* Pro trezor není k dispozici žádné nastavení diagnostiky.
* Pro úschovnu jsou k dispozici diagnostická nastavení, ale ani jedno z nastavení nemá **všechny** události specifické pro prostředek povoleny s cílem LA a v přepínači vybrané **specifické pro prostředek.** 

Takže i v případě, že uživatel má trezor s AzureBackupReport událost povolena v režimu AzureDiagnostics (který je podporován sestavy zálohování), úloha nápravy bude stále platit pro tento trezor, protože režim specifický pro prostředek je doporučený způsob vytváření nastavení diagnostiky, [do budoucna](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event).

Dále pokud má uživatel trezor s pouze podmnožinou šesti událostí specifických pro daný prostředek, bude pro tento trezor platit nápravná úloha, protože sestavy zálohování budou fungovat podle očekávání pouze v případě, že jsou povoleny všechny šest událostí specifických pro daný prostředek.

> [!NOTE]
>
> Pokud má úschovna existující nastavení diagnostiky s povolenou **podmnožinou kategorií specifických pro daný prostředek,** nakonfigurovanou pro odesílání dat do určitého pracovního prostoru LA, řekněme "Pracovní prostor X", pak se nápravná úloha nezdaří (pouze pro tento trezor), pokud je cílový pracovní prostor LA k dispozici v přiřazení zásad **stejný** "Pracovní prostor X". 
>
>Důvodem je, že pokud se události povolené dvěma různými nastaveními diagnostiky ve stejném prostředku **překrývají** v nějaké formě, pak nastavení nemůže mít stejný pracovní prostor LA jako cíl. Tuto chybu budete muset vyřešit ručně tak, že přejdete do příslušného trezoru a nakonfigurujete diagnostické nastavení s jiným pracovním prostorem LA jako cílem.
>
> Všimněte si, že nápravná úloha **se nezdaří,** pokud existující nastavení diagnostiky jako pouze AzureBackupReport povoleno s workspace X jako cíl, protože v tomto případě nebude existovat žádné překrytí mezi událostmi povolenými existující nastavení a události povolené nastavení vytvořené nápravné úlohy.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak používat sestavy zálohování](https://docs.microsoft.com/azure/backup/configure-reports)
* [Další informace o zásadách Azure](https://docs.microsoft.com/azure/governance/policy/)
* [Automatické povolení zálohování pro všechny virtuální počítače v oboru give se používá zásady Azure.](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
