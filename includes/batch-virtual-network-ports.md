- Virtuální síť musí být ve stejné **oblasti** a stejném **předplatném** Azure jako účet Batch.

- Pro fondy vytvořené s použitím konfigurace virtuálního počítače se podporují pouze virtuální sítě založené na Azure Resource Manageru. Pro fondy vytvořené s použitím konfigurace cloudových služeb se podporují pouze klasické virtuální sítě. 
  
- Aby mohl instanční objekt `MicrosoftAzureBatch` používat klasickou virtuální síť, musí mít pro zadanou virtuální síť roli řízení přístupu na základě role `Classic Virtual Machine Contributor` (Přispěvatel virtuálních počítačů modelu Classic). Pokud chcete použít virtuální síť založenou na Azure Resource Manageru, musíte mít oprávnění k přístupu k virtuální síti a nasazování virtuálních počítačů v podsíti.

- Podsíť zadaná pro fond musí obsahovat dostatek nepřiřazených IP adres pro všechny virtuální počítače, na které fond cílí, jejichž počet je součtem vlastností `targetDedicatedNodes` a `targetLowPriorityNodes` fondu. Pokud podsíť nemá dostatek nepřiřazených IP adres, fond částečně přidělí výpočetní uzly a dojde k chybě změny velikosti. 

- Virtuální síť musí umožňovat komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech. To můžete ověřit kontrolou, jestli má virtuální síť přiřazeny nějaké skupiny zabezpečení sítě (NSG). Pokud je komunikace s výpočetními uzly v zadané podsíti zakázaná skupinou NSG, nastaví služba Batch stav výpočetních uzlů na **nepoužitelné**. 

- Pokud má zadaná virtuální síť přiřazené skupiny zabezpečení sítě (NSG) nebo bránu firewall, nakonfigurujte příchozí a odchozí porty podle následujících tabulek:


  |    Cílové porty    |    Zdrojová IP adresa      |   Zdrojový port    |    Přidala služba Batch skupiny NSG?    |    Vyžaduje se pro využitelnost virtuálních počítačů?    |    Akce ze strany uživatele   |
  |---------------------------|---------------------------|----------------------------|----------------------------|-------------------------------------|-----------------------|
  |   <ul><li>Pro fondy vytvořené s konfigurací virtuálního počítače: 29876, 29877</li><li>Pro fondy vytvořené s použitím konfigurace cloudových služeb: 10100, 20100, 30100</li></ul>        |    * nebo můžete zvýšit zabezpečení zadáním IP adres služby Batch. Pokud chcete získat seznam IP adres služby Batch, kontaktujte podporu Azure. | * nebo 443 |    Ano. Batch přidá skupiny NSG na úrovni síťových rozhraní (NIC) připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě povolují přenos jenom z IP adres role služby Batch. I když tyto porty otevřete pro celý web, přenos se bude na síťové kartě blokovat. |    Ano  |  Není nutné zadávat NSG, protože Batch povoluje jenom IP adresy Batch. <br /><br /> Pokud ale NSG zadáte, nezapomeňte prosím zajistit, aby tyto porty byly otevřené pro příchozí přenos. <br /><br /> Pokud jako zdrojovou IP adresu pro NSG zadáte *, Batch přesto přidá skupiny NSG na úrovni síťové karty připojené k virtuálním počítačům. |
  |    3389 (Windows), 22 (Linux)               |    Počítače uživatelů používané pro účely ladění, aby byl možný vzdálený přístup k virtuálnímu počítači.    |   *  | Ne                                    |    Ne                    |    Pokud chcete povolit vzdálený přístup (přes protokol RDP nebo SSH) k virtuálnímu počítači, přidejte skupiny NSG.   |                                


  |    Odchozí porty    |    Cíl    |    Přidala služba Batch skupiny NSG?    |    Vyžaduje se pro využitelnost virtuálních počítačů?    |    Akce ze strany uživatele    |
  |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
  |    443    |    Azure Storage    |    Ne    |    Ano    |    Pokud jste přidali nějaké NSG, ujistěte se, že tento port je otevřený pro odchozí provoz.    |

   Také se ujistěte, že všechny vlastní servery DNS obsluhující virtuální síť dokáží přeložit váš koncový bod služby Azure Storage. Konkrétně musí být možné přeložit adresy URL ve formátu `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net`. 

   Pokud přidáte skupinu zabezpečení sítě založenou na Resource Manageru, můžete využít [značky služeb](../articles/virtual-network/security-overview.md#service-tags) a vybrat pro odchozí připojení IP adresy služby Storage v konkrétní oblasti. Mějte na paměti, že IP adresy služby Storage musí být ve stejné oblasti jako váš účet Batch a virtuální síť. Značky služeb jsou v současné době ve verzi Preview ve vybraných oblastech Azure.