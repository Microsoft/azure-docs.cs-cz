


Tento článek popisuje, jak:

* Vložení dat do Azure virtuální počítač (VM), když se zřizuje.
* Načíst, Windows a Linux.
* Využijte speciální nástroje, které jsou k dispozici v některých systémech ke zjišťování a umožňují automaticky zpracovat vlastní data.

> [!NOTE]
> Tento článek popisuje, jak vlastní data můžete vloženy pomocí virtuálního počítače vytvořeného pomocí modelu nasazení classic. Jak používat rozhraní API pro správu prostředků Azure najdete v tématu [Ukázková šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Vkládání vlastních dat do vašeho virtuálního počítače Azure

[!INCLUDE [classic-cli](contains-classic-cli-content.md)]

Tuto funkci aktuálně podporuje pouze [rozhraní příkazového řádku Azure](https://github.com/Azure/azure-xplat-cli). Tady vytváříme `custom-data.txt` soubor, který obsahuje naše data potom vložit, že k virtuálnímu počítači během zřizování. I když můžete použít některou z možností pro `azure vm create` příkazu následující příklad ukazuje základní jedním z přístupů:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Pomocí vlastních dat na virtuálním počítači
* Pokud váš virtuální počítač Azure je virtuální počítač založený na Windows, pak vlastní datový soubor je uložen na `%SYSTEMDRIVE%\AzureData\CustomData.bin`. I když byla kódováním Base 64 k přenosu z místního počítače do nového virtuálního počítače, je automaticky dekódovaný a může být otevřen nebo okamžitě použít.
  
  > [!NOTE]
  > Pokud soubor existuje, je přepsán. Zabezpečení v adresáři je nastavena na **systému: Úplné řízení** a **Administrators: Úplné řízení**.
  > 
  > 
* Pokud se váš virtuální počítač Azure je virtuálního počítače s linuxem, vlastní datový soubor se nachází v jednom z následujících míst v závislosti na vaší distribuce. Data mohou být kódováním base64, takže možná budete muset nejprve dekódování dat:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Cloud-init na Azure
Pokud je váš virtuální počítač Azure z image Ubuntu nebo CoreOS, můžete použít CustomData odeslat konfiguraci cloudu cloud-init. Nebo pokud vaší vlastní datový soubor skriptu, pak cloud-init můžete spustit ho.

### <a name="ubuntu-cloud-images"></a>Ubuntu cloudových Imagí
Ve většině imagí Azure s Linuxem, upravit "/ etc/waagent.conf" a nakonfigurujte je dočasný prostředek, který disk odkládacího souboru. Zobrazit [uživatelská příručka agenta Azure Linux](../articles/virtual-machines/extensions/agent-linux.md) Další informace.

Cloud imagemi Ubuntu, musí však použít cloud-init, nakonfigurovat disk počítačových prostředků (tedy "dočasné" disk) a Prohodit oddílu. Na stránkách wiki Ubuntu pro další podrobnosti naleznete na následující stránce: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Další kroky: použití cloud-init
Další informace najdete v tématu [cloud-init dokumentaci k Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Přidání odkazu na roli službu Management REST API](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Rozhraní příkazového řádku Azure](https://github.com/Azure/azure-xplat-cli)

