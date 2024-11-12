# Blockchain-Papildoma
# Pirma užduotis


Visų pirma atsisiunčiau Visual Studio 2017 ir 2022 (su 2013 ir 2015 versija buvo problemų instaliuojant, kurių nepavyko išspręsti).
![image](https://github.com/user-attachments/assets/7fc7905a-6f21-418e-aba9-772be8365e4e)


Toliau naudojau githubo linką iš pateiktos papildomos užduoties aprašymo :

https://github.com/libbitcoin/libbitcoin-system
Kopijuoju nuorodą ir cmd įrašau :

![image](https://github.com/user-attachments/assets/e536954e-5932-45d0-92a6-778d4fb38f7d)



Bandymai dirbti su Visual Studio 2017 buvo nesėkmingi, todėl pasirinkau dirbti su 2022.

Toliau reikėjo per Visual Studio 2022 pasirinkti File > Open > Project/Solution randu kelią iki libbitcoin-system.sln (libbitcoin-system\builds\msvc\vs2022), atidariau solution failą ir spaudžiau Build > Build Solution. Susidūriau su NuGet package missing klaida ir turėjau atlikti šiuos veiksmus: Tools > Options > NuGet Package Manager > General ir pažymėti laukelius: 
- Automatically check for missing packages during build;
- Allow NuGet to download missing packages during build;

Tada dešiniuoju klavišu spaudžiau Solution, Solution Explorer langelyje ir pasirinkau Restore NuGet Packages.

Keletą minučių palaukus galima iš naujo pabuildinti solution'ą: Build > Build Solution. Šį kartą klaidų nebebuvo.

Atidarau cmd, nurodau kelią iki aplankalo, kuriame yra build_all.bat failas (libbitcoin-system\build\msvc\build) ir paleidžiu jį.
Man pranešama, kad neatpažįsta nuget.exe ir negali atsiųsti visų reikalingų paketų.
Randu kelią kur yra nuget.exe failas, search bare įvedu environment variables:
![image](https://github.com/user-attachments/assets/806d39f3-544b-42fe-ab57-d1ed6dc0195c)

Toliau spaudžiu Environment Variables > System Variables > Path > Edit :

![image](https://github.com/user-attachments/assets/aeda8d6f-0bc7-468a-9434-e358fcfa9395)

Spaudžiame New ir pridedame kelią iki nuget.exe.

Kartojame tas pačias instrukcijas su cmd. Atlikus šiuos veiksmus outputas buvo sekantis:

![image](https://github.com/user-attachments/assets/8b797702-976c-49ee-93f0-23c95d41a454)



Dabar galime kurti jau savo programas su <bitcoin/bitcoin.hpp< biblioteka.

Sukūrę naują failą Visual Stuio nurodome per Project Properties - Include Additional directories kelią iki bitcoin.hpp failo

Pateiktas kodas užduoties aprašyme:
<details>
  <summary> Peržiūrėti kodą </summary>

```


  #include <bitcoin/bitcoin.hpp>
  bc::hash_digest create_merkle(bc::hash_list& merkle)
{
 if (merkle.empty())		
  return bc::null_hash;	
 else if (merkle.size() == 1)	
  return merkle[0];	
 while (merkle.size() > 1)
	{		
  if (merkle.size() % 2 != 0)
   merkle.push_back(merkle.back());
   assert(merkle.size() % 2 == 0);
   bc::hash_list new_merkle;
   for (auto it = merkle.begin(); it != merkle.end(); it += 2)
		{
   bc::data_chunk concat_data(bc::hash_size * 2);
   auto concat = bc::serializer<
  decltype(concat_data.begin())>(concat_data.begin());
			concat.write_hash(*it);
			concat.write_hash(*(it + 1));
bc::hash_digest new_root = bc::bitcoin_hash(concat_data);
   new_merkle.push_back(new_root);
		}
  merkle = new_merkle;
		// DEBUG output -------------------------------------
		std::cout << "Current merkle hash list:" << std::endl;
		for (const auto& hash : merkle)
			std::cout << " " << bc::encode_base16(hash) << std::endl;
		std::cout << std::endl;
		// --------------------------------------------------
	}
	// Finally we end up with a single item.
	return merkle[0];
}
int main()
{
	// Transactions hashes from a block (#100 000) to reproduce the same merkle root
	bc::hash_list tx_hashes{ {
	bc::hash_literal("8c14f0db3df150123e6f3dbbf30f8b955a8249b62ac1d1ff16284aefa3d06d87"),
	bc::hash_literal("fff2525b8931402dd09222c50775608f75787bd2b87e56995a7bdd30f79702c4"),
	bc::hash_literal("6359f0868171b1d194cbee1af2f16ea598ae8fad666d9b012c8ed2b79a236ec4"),
	bc::hash_literal("e9a66845e05d5abc0ad04ec80f774a7e585c6e8db975962d069a522137b80c1d"),
	} };
	const bc::hash_digest merkle_root = create_merkle(tx_hashes);
	std::cout << "Merkle Root Hash: " << bc::encode_base16(merkle_root) << std::endl;
	// std::cout << "Merkle Root Hash-2: " << bc::encode_hash(merkle_root) << std::endl;
	return 0;
}
 ```

</details>

Bandau buildinti programą, bet susiduriu su klaidomis:

![image](https://github.com/user-attachments/assets/b56dc781-a9db-419f-82a8-0a650f32e41a)

Githubo repozitorijoje nebuvo failo bitcoin.hpp, todėl banday keisti pirmą eilutę į #include <bitcoin/system.hpp>

![image](https://github.com/user-attachments/assets/3f8a3357-7d18-4c22-85ac-86b2d60db516)

Bandau išspręsti šį errorą:

![image](https://github.com/user-attachments/assets/7eb9c1d4-9d8f-4026-b630-ae3c5394e7bd)

Per Project Properties pakeičiu standartą iš default c++14 į c++20:

![image](https://github.com/user-attachments/assets/89ed0a07-73ac-482a-a672-645288d6d646)

Po šio pakeitimo tas pats erroras nedingo, tik prisidėjo 80 naujų klaidų.

![image](https://github.com/user-attachments/assets/9ac5bed8-8cc4-48fb-a0a0-094d71e0ece7)

cmd
wsl --install

Atidarom Ubuntu

![image](https://github.com/user-attachments/assets/75a4d099-047f-4c74-8e24-de198889dd2a)

paleidau ./install.sh --prefix=/home/paulina/myprefix --build-boost --disable-shared
![image](https://github.com/user-attachments/assets/28dced59-ddd2-4b15-8231-f17bf96789c0)

![image](https://github.com/user-attachments/assets/04969fcf-4db0-4a2d-a035-248e187e27cd)



# Antra papildoma
https://bitcoincore.org/en/download/

![image](https://github.com/user-attachments/assets/f0f73129-efbf-4825-98f9-c485c432a4e8)

![image](https://github.com/user-attachments/assets/48e675a4-0baa-4825-b941-62210542df65)

Error reading from database
datadir...
„Bitcoin Core“ veikia lokaliai, nes jam būtina nuolatinė ir greita prieiga prie visų blokų grandinės duomenų, o tai su debesijos saugykla būtų neįmanoma. Debesyje saugomi duomenys reikalauja sinchronizacijos, todėl juos pasiekti yra lėčiau ir nepatikimiau, o tai stabdytų ir trukdytų „Bitcoin Core“ veiklai.
Kelios dešimtys bandymų rodyti direktorijas konfiguracijos failuose, reindeksuoti iš naujo, daryti "Free up space" One Drive folderyje buvo nesėkmingi, vis susidurdavau su naujom problemom pvz.: "Errors reading from database" arba "Bitcoin Network error" ir t.t.


![image](https://github.com/user-attachments/assets/e22fcdd7-3131-4e9e-81eb-e65d7a8d3e43)




# Trečia papildoma

Prisijungiame prie VU bitcoin mazgo per PuTTY: 158.129.140.201:3536

 ## Diegimas python-bitcoinlib
 VU mazge turėtų būti įdiegta python-bitcoinlib biblioteka, bet rodoma klaida:
 neatpažintas "bitcoin", todėl pabandome įdiegti:


![image](https://github.com/user-attachments/assets/d2b69927-310a-41f7-b2dd-a66c771e616c)


Įrašau pip install python-bitcoinlib:


![image](https://github.com/user-attachments/assets/8f8a4ae1-e581-4abf-b2fe-654aa24698b0)

 Dabar galime tęsti.

 ## Bandymas su pavyzdžiais

 Terminale suvedame : nano rpc_example.py
 Įklijuojame pateiktą kodą: 
<details>
	<summary>Peržiūrėti rpc_example.py</summary>
	
from bitcoin.rpc import RawProxy
# Create a connection to local Bitcoin Core node
p = RawProxy()
# Run the getblockchaininfo command, store the resulting data in info
info = p.getblockchaininfo()
# Retrieve the 'blocks' element from the info
print(info['blocks'])
</details>

Spaudžiame Ctrl + x :

![image](https://github.com/user-attachments/assets/e8927d91-50b0-4ea6-8e53-4384dbace83d)

Įrašome Y ir spaudžiame Enter.

![image](https://github.com/user-attachments/assets/1f9e4cff-e098-4153-8be9-beae499dadab)

Programos paleidimui suvedame : python3 rpc_example.py
Gauname atsakymą:

![image](https://github.com/user-attachments/assets/4d95a5ab-96eb-4726-9ebe-cd0afa805183)


Kartojame tuos pačius žingsnius su rpc_transaction.py ir rpc_block.py pavyzdžiais:


<details>
		<summary>Peržiūrėti rpc_transaction.py</summary>
from bitcoin.rpc import RawProxy
# Create a connection to local Bitcoin Core node
p = RawProxy()
# Alice's transaction ID
txid = "0627052b6f28912f2703066a912ea577f2ce4da4caa5a5fbd8a57286c345c2f2"
# First, retrieve the raw transaction in hex
raw_tx = p.getrawtransaction(txid)
# Decode the transaction hex into a JSON object
decoded_tx = p.decoderawtransaction(raw_tx)
# Retrieve each of the outputs from the transaction
for output in decoded_tx['vout']:
 print(output['scriptPubKey']['address'], output['value'])
</details>

Atsakymas:

 ![image](https://github.com/user-attachments/assets/a9178714-89cb-4c0a-b2cd-4ccb2bbbd42d)


<details>
		<summary>Peržiūrėti rpc_block.py</summary>
from bitcoin.rpc import RawProxy
# Create a connection to local Bitcoin Core node
p = RawProxy()
# The block height where Alice's transaction was recorded
blockheight = 277316
# Get the block hash of block with height 277316
blockhash = p.getblockhash(blockheight)
# Retrieve the block by its hash
block = p.getblock(blockhash)
# Element tx contains the list of all transaction IDs in the block
transactions = block['tx']
block_value = 0
# Iterate through each transaction ID in the block
for txid in transactions:
 tx_value = 0
 # Retrieve the raw transaction by ID
 raw_tx = p.getrawtransaction(txid)
 # Decode the transaction
 decoded_tx = p.decoderawtransaction(raw_tx)
 # Iterate through each output in the transaction
 for output in decoded_tx['vout']:
 # Add up the value of each output
 	tx_value = tx_value + output['value']
 # Add the value of this transaction to the total
 block_value = block_value + tx_value
print("Total output value (in BTC) in block #277316: ", block_value)
</details>

Atsakymas:

![image](https://github.com/user-attachments/assets/e895b43d-351f-49a7-9a0e-52ff3280c6e0)


Kaip veikia?

Naudodami RawProxy siunčiame įvairias užklausas, pvz.:

 - getblockcount() – grąžina dabartinį blokų aukštį.
 - getblockhash(height) – grąžina bloko hash pagal aukštį.
 - getblock(hash) – grąžina bloko informaciją pagal hash.
 - getrawtransaction(txid) – grąžina transakcijos informaciją pagal ID.
 - decoderawtransaction(raw_tx) – iškoduoja neapdorotą transakciją.
ir kt.

Šie metodai leidžia gauti išsamią informaciją apie blockchain būklę ir atlikti operacijas su Bitcoin transakcijomis tiesiogiai python programos.


## Apskaičiuojamas transakcijos mokestis

Pridėjau programą, kuri apskaičiuoja Bitcoin transakcijos mokestį pagal jos hash'ą:

<details>
	<summary>Peržiūrėti programą</summary>
from bitcoin.rpc import RawProxy
p = RawProxy()
txid = "84e3eea9920092343ae000931a002b8225ecea919fbe452d33080676d042021f"
raw_tx = p.getrawtransaction(txid)
decoded_tx = p.decoderawtransaction(raw_tx)
total_input = 0
for vin in decoded_tx['vin']:
 input_txid = vin['txid']
 input_vout = vin['vout']
 raw_input_tx = p.getrawtransaction(input_txid)
 decoded_input_tx = p.decoderawtransaction(raw_input_tx)
 total_input += decoded_input_tx['vout'][input_vout]['value']
total_output = sum(output['value'] for output in decoded_tx['vout'])
tx_mokestis = total_input - total_output
print(f"Transakcijos mokestis: {tx_mokestis} BTC")
</details>

Patikriname pvz.:

https://www.blockchain.com/explorer/blocks/btc/868177

Panaudoju šį hash : 84e3eea9920092343ae000931a002b8225ecea919fbe452d33080676d042021f

Outputas turėtų būti : 0.00004389 BTC

![image](https://github.com/user-attachments/assets/93e89a5c-ef1b-4ff5-9168-ace9f1f99f1c)

Mano outputas:

![image](https://github.com/user-attachments/assets/4d1e7d90-94e1-43f8-93a5-fb31a501ba0e)


Išbandau programą su 2019-09-06 įvykusia viena vertingiausių transakcija:
Transakcijos hash : 4410c8d14ff9f87ceeed1d65cb58e7c7b2422b2d7529afc675208ce2ce09ed7d
Outputas : 0.00004389 BTC

Mano outputas:
    
![image](https://github.com/user-attachments/assets/608e8bbe-2774-4510-b77b-13b5713fd5be)

## Bloko hash'o patikrinimas



