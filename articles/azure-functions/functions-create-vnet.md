---
title: Integrace funkcí Azure s virtuální sítí Azure
description: Podrobný kurz, který ukazuje, jak připojit funkci k virtuální síti Azure
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433217"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Kurz: Integrace služby Functions s virtuální sítí Azure

Tento kurz ukazuje, jak používat Funkce Azure pro připojení k prostředkům ve virtuální síti Azure. vytvoříte funkci, která má přístup k internetu i k virtuálnímu počítači se systémem WordPress ve virtuální síti.

> [!div class="checklist"]
> * Vytvoření aplikace funkcí v plánu Premium
> * Nasazení webu WordPress do virtuálního počítače ve virtuální síti
> * Připojení aplikace funkce k virtuální síti
> * Vytvoření automatického proxy serveru pro přístup k prostředkům WordPress
> * Žádost o soubor WordPress zevnitř virtuální sítě

## <a name="topology"></a>Topologie

Následující diagram znázorňuje architekturu řešení, které vytvoříte:

 ![UI pro integraci virtuální sítě](./media/functions-create-vnet/topology.png)

Funkce spuštěné v plánu Premium mají stejné možnosti hostování jako webové aplikace ve službě Azure App Service, která zahrnuje funkci integrace virtuální sítě. Další informace o integraci virtuální sítě, včetně řešení potíží a pokročilé konfigurace, najdete [v tématu Integrace aplikace s virtuální sítí Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu je důležité, abyste pochopili adresování IP adres a podsíťování. Můžete začít s [tímto článkem, který pokrývá základy adresování a podsíťování](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Mnoho dalších článků a videí jsou k dispozici on-line.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-a-function-app-in-a-premium-plan"></a>Vytvoření aplikace funkcí v prémiovém tarifu

Nejprve vytvoříte aplikaci funkcí v [plánu Premium]. Tento plán poskytuje škálování bez serveru a zároveň podporuje integraci virtuální sítě.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Aplikaci funkce můžete připnout k řídicímu panelu výběrem ikony špendlíku v pravém horním rohu. Připnutí usnadňuje návrat k této aplikaci funkce po vytvoření virtuálního počítače.

## <a name="create-a-vm-inside-a-virtual-network"></a>Vytvoření virtuálního počítače ve virtuální síti

Dále vytvořte předkonfigurovaný virtuální počítač, který běží WordPress uvnitř virtuální sítě[(WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). WordPress VM se používá kvůli jeho nízké náklady a pohodlí. Stejný scénář funguje s libovolným prostředkem ve virtuální síti, jako jsou rest api, prostředí služby App Service a další služby Azure. 

1. Na portálu zvolte + Vytvořit zdroj v levém **navigačním** podokně, v typu `WordPress LEMP7 Max Performance`vyhledávacího pole a stiskněte Enter.

1. Ve výsledcích vyhledávání zvolte **Wordpress LEMP Max Performance.** Vyberte softwarový plán **Wordpress LEMP Maximální výkon pro CentOS** jako **softwarový plán** a vyberte **Vytvořit**.

1. Na kartě **Základy** použijte nastavení virtuálního počítače, jak je uvedeno v tabulce pod obrázkem:

    ![Karta Základy pro vytvoření virtuálního virtuálního mísy](./media/functions-create-vnet/create-vm-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým jsou vytvořeny prostředky. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Zvolte `myResourceGroup`nebo skupinu prostředků, kterou jste vytvořili pomocí aplikace pro funkci. Použití stejné skupiny prostředků pro aplikaci funkce, WordPress VM a hostingový plán usnadňuje vyčištění prostředků, když jste hotovi s tímto kurzem. |
    | **Název virtuálního počítače** | VNET-Wordpress | Název virtuálního_ virtuálního_ |
    | **[Oblasti](https://azure.microsoft.com/regions/)** | (Evropa) Západní Evropa | Vyberte oblast ve vašem okolí nebo v blízkosti funkcí, které přistupují k virtuálnímu virtuálnímu virtuálnímu zařízení. |
    | **Velikost** | B1s | Zvolte **Změnit velikost** a pak vyberte standardní obraz B1s, který má 1 virtuální procesor a 1 GB paměti. |
    | **Typ ověřování** | Heslo | Chcete-li použít ověřování hesla, musíte také zadat **uživatelské jméno**, zabezpečené **heslo**a potom **potvrdit heslo**. V tomto kurzu se nebudete muset přihlásit k virtuálnímu virtuálnímu virtuálnímu mísu, pokud nebudete muset řešit potíže. |

1. Zvolte kartu **Síť** a v části Konfigurovat virtuální sítě vyberte **Vytvořit nový**.

1. V **části Vytvořit virtuální síť**použijte nastavení v tabulce pod obrázkem:

    ![Karta Vytváření virtuálního účtu k síti](./media/functions-create-vnet/create-vm-2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název** | síť myResourceGroup-vnet | Můžete použít výchozí název generovaný pro vaši virtuální síť. |
    | **Rozsah adres** | 10.10.0.0/16 | Pro virtuální síť použijte jeden rozsah adres. |
    | **Název podsítě** | Výukový program-Net | Název podsítě. |
    | **Rozsah adres** (podsíť) | 10.10.1.0/24   | Velikost podsítě definuje, kolik rozhraní lze do podsítě přidat. Tato podsíť je používána webem WordPress.  Podsíť `/24` obsahuje 254 adres hostitele. |

1. Chcete-li vytvořit virtuální síť, vyberte **OK.**

1. Zpět na kartě **Síť** zvolte **Žádný** pro **veřejnou IP adresu**.

1. Zvolte kartu **Správa** a potom v **účtu úložiště Diagnostika**zvolte účet úložiště, který jste vytvořili pomocí aplikace funkcí.

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **Vytvořit**. Proces vytváření virtuálního soudu trvá několik minut. Vytvořený virtuální počítač může přistupovat jenom k virtuální síti.

1. Po vytvoření virtuálního počítače zvolte **Přejít na prostředek,** chcete-li zobrazit stránku pro nový virtuální počítač, a pak v části **Nastavení**zvolte **Síť** .

1. Ověřte, zda neexistuje žádná **veřejná IP adresa**. Poznamenejte si **privátní IP**adresu , kterou používáte k připojení k virtuálnímu počítači z aplikace funkce.

    ![Nastavení sítě ve virtuálním počítače](./media/functions-create-vnet/vm-networking.png)

Nyní máte web WordPress nasazený výhradně v rámci vaší virtuální sítě. Tato stránka není přístupná z veřejného internetu.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Připojení aplikace funkce k virtuální síti

S webem WordPress běžícím ve virtuálním počítači ve virtuální síti teď můžete připojit aplikaci funkcí k této virtuální síti.

1. V nové aplikaci funkcí vyberte **možnost Platform features** > **Networking**.

    ![Volba sítě v aplikaci funkcí](./media/functions-create-vnet/networking-0.png)

1. V části **Integrace virtuální sítě**vyberte Klepnutím **sem nakonfigurujete**.

    ![Stav konfigurace síťové funkce](./media/functions-create-vnet/Networking-1.png)

1. Na stránce integrace virtuální sítě vyberte **Přidat virtuální síť (náhled).**

    ![Přidání náhledu integrace virtuální sítě](./media/functions-create-vnet/networking-2.png)

1. Ve **stavu síťovéfunkce**použijte nastavení v tabulce pod obrázkem:

    ![Definování virtuální sítě aplikace pro funkce](./media/functions-create-vnet/networking-3.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Virtuální síť** | Virtuální síť MyResourceGroup | Tato virtuální síť je ta, kterou jste vytvořili dříve. |
    | **Podsíť** | Vytvořit novou podsíť | Vytvořte podsíť ve virtuální síti, aby se vaše funkční aplikace používala. Integrace virtuální sítě musí být nakonfigurovaná tak, aby používala prázdnou podsíť. Nezáleží na tom, že vaše funkce používají jinou podsíť než virtuální počítač. Virtuální síť automaticky směruje provoz mezi dvěma podsítěmi. |
    | **Název podsítě** | Síť funkcí | Název nové podsítě. |
    | **Blok adres virtuální sítě** | 10.10.0.0/16 | Zvolte stejný blok adres, který používá web WordPress. Měli byste mít definován pouze jeden blok adresy. |
    | **Rozsah adres** | 10.10.2.0/24   | Velikost podsítě omezuje celkový počet instancí, na které může vaše aplikace funkce Premium plan škálovat. Tento příklad `/24` používá podsíť s 254 dostupnými adresami hostitele. Tato podsíť je přezřízená, ale snadno se vypočítá. |

1. Výběrem **možnosti OK** přidejte podsíť. Zavřete stránky Integrace virtuální sítě a Stav síťových funkcí a vraťte se na stránku aplikace pro funkce.

Aplikace funkce nyní může přistupovat k virtuální síti, kde je spuštěn web WordPress. Dále použijete [Azure Functions Proxy](functions-proxies.md) vrátit soubor z webu WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Vytvoření proxy serveru pro přístup k prostředkům virtuálních ms

S povolenou integrací virtuální sítě můžete vytvořit proxy server ve vaší funkční aplikaci a předávat požadavky na virtuální počítač spuštěný ve virtuální síti.

1. V aplikaci funkce vyberte **Proxy** > **+**, pak použijte nastavení proxy serveru v tabulce pod obrázkem:

    ![Definování nastavení proxy serveru](./media/functions-create-vnet/create-proxy.png)

    | Nastavení  | Navrhovaná hodnota  | Popis      |
    | -------- | ---------------- | ---------------- |
    | **Název** | Plant | Název může být libovolná hodnota. Používá se k identifikaci proxy. |
    | **Šablona postupu** | /rostlina | Směr, který se mapuje na prostředek virtuálního mísy. |
    | **Adresa URL back-endu** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Nahraďte `<YOUR_VM_IP>` IP adresou vašeho virtuálního počítače WordPress, který jste vytvořili dříve. Toto mapování vrátí jeden soubor z webu. |

1. Vyberte **Vytvořit,** chcete-li do aplikace funkcí přidat proxy server.

## <a name="try-it-out"></a>Vyzkoušejte si to.

1. V prohlížeči zkuste získat přístup k adrese URL, kterou jste použili jako **adresu URL back-endu**. Podle očekávání časový čas požadavku. Časový režim nastane, protože vaše stránky WordPress jsou připojeny pouze k vaší virtuální síti a ne k internetu.

1. Zkopírujte hodnotu **adresy URL proxy** z nového proxy serveru a vložte ji do adresního řádku prohlížeče. Vrácený obrázek je z webu WordPress běžícího uvnitř vaší virtuální sítě.

    ![Soubor obrázku rostliny vrácený z webu WordPress](./media/functions-create-vnet/plant.png)

Vaše aplikace funkcí je připojena k internetu i k virtuální síti. Proxy server přijímá požadavek přes veřejný internet a pak funguje jako jednoduchý proxy server HTTP pro předání tohoto požadavku do připojené virtuální sítě. Proxy pak relé odpověď zpět na vás veřejně přes internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu web WordPress slouží jako rozhraní API, které se nazývá pomocí proxy serveru v aplikaci funkce. Tento scénář je dobrý kurz, protože je snadné nastavit a vizualizovat. Můžete použít jakékoli jiné rozhraní API nasazené v rámci virtuální sítě. Mohli jste také vytvořit funkci s kódem, který volá api nasazená v rámci virtuální sítě. Realističtější scénář je funkce, která používá data klienta API pro volání instance SERVERU SQL Server nasazené ve virtuální síti.

Funkce spuštěné v plánu Premium sdílejí stejnou základní infrastrukturu služby App Service jako webové aplikace v plánech PremiumV2. Veškerá dokumentace k [webovým aplikacím ve službě Azure App Service](../app-service/overview.md) se vztahuje na funkce plánu Premium.

> [!div class="nextstepaction"]
> [Další informace o možnostech vytváření sítí ve funkcích](./functions-networking-options.md)

[Plán Premium]: functions-scale.md#premium-plan
