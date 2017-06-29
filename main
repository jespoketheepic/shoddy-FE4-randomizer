// basic file operations
#include <iostream>
#include <fstream>
#include <stdlib.h>     /* srand, rand */
#include <time.h>       /* time */
#include <string>
#include <bitset>
using namespace std;

// Sigurd's base HP.
// Base HP is my waypoint
long baseposition = 242280;
// Add 0x26 to get to next character
int characteroffset = 38;
// Offset from Base HP to get to HP Growth
int growthoffset = 19;
// Offset from Base HP to get to Skill 1
int skilloffset = 27;
// Independent waypoint for holy blood, centered on Baldur's HP growth.
int holybloodbaseposition = 231909;
// Offset from holybloodbase for next entry
int holybloodoffset = 16;
// Offset from holybloodbase to get to holyweapon Str bonus. There are only 6 stats to boost here.
int holyweaponoffset = 8;

int main () {
	
	/* initialize random seed: */
  srand (time(NULL));
	
	// Open stream to database file (ROM by the name of FE4)
	fstream database;
	database.open ("FE4.sfc", ios::out | ios::in | ios::binary);
	// I should get to implementing this at some point
	if (database.is_open()) 
	{ /* ok, proceed with output */ }

// Open stream to name table
	ifstream nametable;
	nametable.open ("FE4 name list.txt", ios::in);
	// I should get to implementing this at some point
	if (nametable.is_open()) 
	{ /* ok, proceed with output */ }

// Open stream to settings
	ifstream settingsdoc;
	settingsdoc.open ("Settings.txt", ios::in);
	// I should get to implementing this at some point
	if (settingsdoc.is_open()) 
	{/* ok, proceed with output */ }
	
	// Open stream to log file
	ofstream log;
	log.open ("FE4 randomizer growth log.txt", ios::out | ios::trunc);
	// I should get to implementing this at some point
	if (log.is_open()) 
	{ /* ok, proceed with output */ }

// Load the settings
	int settings[6];
	for(int i=0; i<6; i++)
	{
		char readbuffer = 0;
		int readbufferInt = 0;
		settingsdoc.seekg(i);
		settingsdoc.read(&readbuffer, sizeof(readbuffer));
		readbufferInt = readbuffer;
		settings[i] = readbufferInt - 48; // -48 to make a ascii number into its numerical value
	}
	
	
	// -------------------- Holy Blood -------------------------

	// int Baldur[8];
	// int Naga[8];
	// int Dain[8];
	// int Nova[8];
	// int Odo[8];
	// int Ullir[8];
	// int Neir[8];
	// int Fala[8];
	// int Tordo[8];
	// int Forsety[8];
	// int Blagi[8];
	// int Hezul[8];
	// int Loptyr[8];
	
	int HolyBlood[13][8] = {}; // This is declared for all of main so i can use it much later on.

		for(int i=0; i<13; i++)
		{			
			if(settings[4] > 0) // Randomizing Holy Blood
			{
				int distribution [8] = {}; // These are here because this is a poorly planned extention of the program.
				int distributiontotal = 0;
				int growthtotal = 40; // We know that when halving HP growth contributions, Naga, Blagi and Loptyr total 45 and all others 40
				int arraytotal = 0;
				int buffer = 0;
				
				if(i == 1 || i == 10 || i == 12)
				{
					growthtotal = 45;
				}
				
				for(int j=0; j<8; j++)
				{
					// Distribution randomizer
					// Generate number between 1 and 1000
					distribution[j] = rand() % 1000 + 1;
					distributiontotal += distribution[j];
				}
				
				// Stat distribution 
				for(int j=0; j<8; j++)
				{
					HolyBlood[i][j] += distribution[j]*growthtotal/distributiontotal;
					HolyBlood[i][j] /= 5; // Trim the fat
					HolyBlood[i][j] *= 5; // So we can get pretty 5-divisible growth rates
					arraytotal += HolyBlood[i][j];
				}
				// Dump truncated decimals in HP
				HolyBlood[i][0] += (growthtotal - arraytotal);
				// Redouble HP growth
				HolyBlood[i][0] *= 2;
				
				for(int j=1; j<8; j++) // Cut down non-HP growths over the cap
				{
					if(HolyBlood[i][j]>30)
					{
						buffer += HolyBlood[i][j] - 30;
						HolyBlood[i][j] = 30;
					}
				}
				
				HolyBlood[i][0] += buffer*2; //Dump remainder in HP again
				
				while(HolyBlood[i][0]>30) //excess HP growth is randomly put back in other stats
				{
					int j = rand() % 7 + 1;
					if(HolyBlood[i][j]<30) // Doesn't move on if target stat is capped
					{
						HolyBlood[i][0] -= 10; // Remember the double HP rule
						HolyBlood[i][j] += 5;
					}
				}
				
				// Write Growths to file
				for(int j=0; j<8; j++)
				{
					char writebuffer = HolyBlood[i][j];
					database.seekp(holybloodbaseposition+(i*holybloodoffset)+j);
					database.write(&writebuffer, sizeof(writebuffer));
				}
			
			}
			else // When not randomizing them, we need to fetch them to add to the growth log
			{
				for(int j=0; i<8; j++)
				{
					char readbuffer = 0;
					int readbufferInt = 0;
					database.seekg(holybloodbaseposition+(holybloodoffset*i)+j);
					database.read(&readbuffer, sizeof(readbuffer));
					readbufferInt = readbuffer;
					HolyBlood[i][j] = readbufferInt;
				}
			}
		}
	
	// -------------------- Holy Weapon Bonus --------------------
	
	if(settings[5]>0)
	{
		for(int i=0; i<13; i++)
		{
			int total = 0;
			int stats[6] = {};
			int distribution [6] = {}; // These are here because this is a poorly planned extention of the program.
			int distributiontotal = 0;
			int arraytotal = 0;
			
			if(i==0) // Tyrfing
			{
				if(settings[5] > 1) // skip if we have picked safe mode
				{
					total = 40;
				}
			}
			else if(i==1) // Book of Naga
			{
				if(settings[5] > 1) // skip if we have picked safe mode
				{
					total = 80;
				}
			}
			else if(i==12) // Loptyr
			{
				total = 5;
			}
			else // Everything else
			{
				total = 30;
			}
			
			
			// Distribution randomizer (Only 6 stats this time)
			for(int j=0; j<6; j++)
			{
			// Generate number between 1 and 1000
				distribution[j] = rand() % 1000 + 1;
				distributiontotal += distribution[j];
			}
				
			// Stat distribution
			for(int j=0; j<6; j++)
			{
				stats[j] = distribution[j]*total/distributiontotal;
				stats[j] /= 5;
				stats[j] *= 5;
				arraytotal += stats[j];
			}

			// Dump truncated decimals
			while((total - arraytotal)>0)
			{
				int j = rand() % 6 + 0;
				stats[j] += 5;
				arraytotal += 5;
			}
				
			// Write to file
			for(int j=0; j<6; j++)
			{
				char writebuffer = HolyBlood[i][j];
				database.seekp(holybloodbaseposition+(i*holybloodoffset)+holyweaponoffset+j);
				database.write(&writebuffer, sizeof(writebuffer));
			}
		}
	}
	
	
	// ------------ Character stuff ----------------
	
// Go through every character
	for(int character=0; character<=45; character++)
	{
		int offset = character; //This variable is in charge of making us skip the character entries that are enemies (Applies to FE3)
		int bonusoffset = 0; // This compensates for the holes in the character list that probably control the substitute characters or something.
		if(character>23)
		{
			bonusoffset += 12;
			if(character>26)
			{
				bonusoffset += 12;
				if(character>40)
				{
					bonusoffset += 12;
				}
			}
		}
		int stattotal = 0;
		int growthtotal = 0;
		int distribution [8];
		int distributiontotal = 0;
		int stats [8];
		int statsarraytotal = 0;
		int hasholyblood[32] = {}; // I only ever need 26 of these, but just in case someone opens a wrong file with this program, i don't want it breaking more than neccesary.
		
		if(character == 24) // Generation divider
		{
			log << "----------------------------------------\n\n";
		}
		// Print name in log file
		for(int i=0; i<=9; i++)
		{
			char namebuffer;
			nametable.seekg((character*10)+i);
			nametable.read(&namebuffer, sizeof(namebuffer));
			log << namebuffer;
		}
		
		// Print stat indincators
		log << "\n|  HP | Str | Mag | Skl | Spd | Lck | Def | Res |\n|";
		//-------------------- Base Stats --------------------
		
		// Check if we want random bases
		
		if(settings[0] > 0)
		{
		// Find base stat total
		for(int i=1; i<8; i++) // i starts at 1 to skip HP to avoid wonky stuff.
		{
			char readbuffer = 0;
			unsigned char readbufferUnsigned = 0;
			int readbufferInt = 0;
		database.seekg(baseposition+(offset*characteroffset)+bonusoffset+i);
		database.read(&readbuffer, sizeof(readbuffer));
		readbufferUnsigned = readbuffer;
		readbufferInt = readbufferUnsigned;
		stattotal += readbufferInt;
		}
		
		// Distribution randomizer (Here used for Base Stats)
		for(int i=1; i<8; i++)
		{
			// Generate number between 1 and 1000
				distribution[i] = rand() % 1000 + 1;
				distributiontotal += distribution[i];
		}
				
		// Stat distribution
		for(int i=1; i<8; i++)
		{
		stats[i] = distribution[i]*stattotal/distributiontotal;
		statsarraytotal += stats[i];
		}

		// Dump truncated decimals
		while((stattotal - statsarraytotal)>0)
		{
			int j = rand() % 7 + 1;
			stats[j] += 1;
			statsarraytotal += 1;
		}
		
		// Check if stat is too high (Skips HP because higher cap)
		for(int i=1; i<8; i++)
		{
			while(stats[i] > 20)
			{
				// Too high stats are shaved off and handed to a random stat
					stats[i] = stats[i] - 1;
					int j = rand() % 7 + 1;
					stats[j] += 1;
			}
		}
		
		// Write Base Stats to file
		for(int i=1; i<8; i++)
		{
			char writebuffer = stats[i];
		database.seekp(baseposition+(offset*characteroffset)+bonusoffset+i);
		database.write(&writebuffer, sizeof(writebuffer));
		}
		}
		
		//-------------------- Growths --------------------
		
		// Reset variables
		distributiontotal = 0;
		statsarraytotal = 0;
		growthtotal = 0;
		
		// Check if we want random growths
		if(settings[1] > 0)
		{
		
		// Find growth total
		for(int i=0; i<8; i++)
		{
			char readbuffer = 0;
			unsigned char readbufferUnsigned = 0;
			int readbufferInt = 0;
			database.seekg(baseposition+(offset*characteroffset)+bonusoffset+growthoffset+i);
			database.read(&readbuffer, sizeof(readbuffer));
			readbufferUnsigned = readbuffer;
			readbufferInt = readbufferUnsigned;
			if(i == 0) // HP growth counts for half here and is redoubled at the end.
			{
				readbufferInt = readbufferInt / 2;
			}
			growthtotal += readbufferInt;
		}
		
		// Distribution randomizer
		for(int i=0; i<8; i++)
		{
			// Generate number between 1 and 1000
				distribution[i] = rand() % 1000 + 1;
				distributiontotal += distribution[i];
		}
		
		// Growth distribution
		for(int i=0; i<8; i++)
		{
		stats[i] = distribution[i]*growthtotal/distributiontotal;
		stats[i] /= 5; // These two lines floor the numbers to multiples of 5 
		stats[i] *= 5;
		statsarraytotal += stats[i];
		}

		// Dump truncated decimals
		while((growthtotal - statsarraytotal)>0)
		{
			int j = rand() % 8 + 0;
			stats[j] += 5;
			statsarraytotal += 5;
		}
		
		// Redouble HP
		stats[0] = stats[0] * 2;
		
		// Check if any stat is too high
		// As long as any stat is too high, it will keep rolling
		while(stats[0]>150 || stats[1]>150 || stats[2]>150 || stats[3]>150 || stats[4]>150 || stats[5]>150 || stats[6]>150 || stats[7]>150)
		{
			// Each stat gets their turn
			for(int i=0; i<8; i++)
			{	
				while(stats[i] > 150)
				{
					// Too high stats are shaved off and handed to a random stat
					int j = rand() % 8 + 0;
					stats[i] -= 5;
					if(i=0)
					{
						stats[i] -= 5;
					}
					stats[j] += 5;
					if(j=0)
					{
						stats[j] += 5;
					}
				}
			}
		}

		
		// Write Growths to file
		for(int i=0; i<8; i++)
		{
			char writebuffer = stats[i];
		database.seekp(baseposition+(offset*characteroffset)+bonusoffset+growthoffset+i);
		database.write(&writebuffer, sizeof(writebuffer));
		}
		
		
		// Find Holy Blood
		for(int i=0; i<4; i++)
		{
			char readbuffer = 0;
			unsigned char readbufferUnsigned = 0;
			unsigned int readbufferInt = 0;
			database.seekg(baseposition+(offset*characteroffset)+bonusoffset+skilloffset+3+i); // Skilloffset +3 is holy blood 1
			database.read(&readbuffer, sizeof(readbuffer));
			readbufferUnsigned = readbuffer;
			readbufferInt = readbufferUnsigned;
			if(readbufferInt > 0)
			{
				for(int j=0; j<8; j++)
				{
					std::bitset<8> bits(readbufferInt); // Holy Blood allocation is stored bitwise (Skills are too, but i already made that part, and i only just read up on bitwise stuff now)
					hasholyblood[i*8+j] = bits.test(j);
				}
			}
		}
		}
		
		// Add Holy Blood bonuses (for the Log)
		for(int i=0; i<13; i++) // Check all blood types
		{
			// Add minor blood
			if(hasholyblood[i*2]>0)
			{
				for(int j=0; j<8; j++)
				{
					stats[j] += HolyBlood[i][j];
				}
			}	
			// Add major blood
			if(hasholyblood[i*2+1]>0)
			{
				for(int j=0; j<8; j++)
				{
					stats[j] += HolyBlood[i][j]*2;
				}
			}
		}
		
		// Writing Growths to the log
		for(int i=0; i<8; i++)
		{
			int k = i;
			if(i==5) // Put Luck 6th
			{
				k = 7;
			}
			
			else if(i>5) // The last ones are moved on
			{
				k = i - 1;
			}
		
		// Determining nr of digits
		int length = 1;
		int statsclone = stats[k];
		while ( statsclone /= 10 )
		{
			length++;
		}
		// Fill out with spaces
		for(int j=0; j<(3-length); j++)
		{
			log << " ";
		}
		// Write the numbers to the log
			std::string textbuffer = std::to_string(stats[k]);
			log << textbuffer;
			log << "% |";
		}
		log << "\n\n";
		
		
		
		//-------------------- Skills --------------------
		
		// Check if we want random skills
		if(settings[2] > 0)
		{
			int skillsdata[3] = {0,0,0};
			int skills[3] = {0,0,0};
			// Add how many raffle tickets Pursuit gets
			int skillcount = 14 + settings[3];
			
			// If we want everyone to have Pursuit, do that.
			if(settings[3] == 0)
			{
				skills[0] = 15;
			}
			else
			{
				// Generate number between 1 and the skill count
				skills[0] = rand() % skillcount + 1;
				if(skills[0] > 15)
				{
					skills[0] = 15;
				}
			}
			
			for(int i=1; i<3; i++)
			{
				if(settings[2] > i)
				{
					// Generate number between 0 and the skill count
					skills[i] = rand() % skillcount + 1;
					if(skills[i] > 15)
					{
						skills[i] = 15;
					}
					if(character==39 && i==2) // Give Julia Nihil
					{
						skills[2] = 4;
					}
					// Eliminate duplicates
					while(skills[i] == skills[0])
					{
						skills[i] = rand() % skillcount + 1;
						if(skills[i] > 15)
						{
							skills[i] = 15;
						}
					}
				}
				else
				{
					skills[i] = 0;
					if(character==39 && i==2) // Give Julia Nihil. Yes, even when everyone else gets only 1 skill Julia gets Nihil in slot 2, otherwise it would interfere with "everybody Pursuit" mode.
					{
						skills[2] = 4;
					}
				}
			}
			
			// Eliminate duplicates
			if(skills[2] != 0)
			{
				while(skills[2] == skills[1] || skills[2] == skills[0])
				{
					skills[2] = rand() % skillcount + 1;
					if(skills[2] > 15)
					{
						skills[2] = 15;
					}
				}
			}
			
			// Convert to weird bit format
			for(int i=0; i<3; i++)
			{	
				if(skills[i] == 1) // Wrath
				{
					skillsdata[0] += 1;
				}
				else if(skills[i] == 15) // Pursuit
				{
					skillsdata[0] += 2;
				}
				else if(skills[i] == 2) // Adept
				{
					skillsdata[0] += 4;
				}
				else if(skills[i] == 3) // Charisma
				{
					skillsdata[0] += 16;
				}
				else if(skills[i] == 4) // Nihil
				{
					skillsdata[0] += 64;
				}
				else if(skills[i] == 5) // Miracle
				{
					skillsdata[0] += 128;
				}
				else if(skills[i] == 6) // Critical
				{
					skillsdata[1] += 1;
				}
				else if(skills[i] == 7) // Vantage
				{
					skillsdata[1] += 2;
				}
				else if(skills[i] == 8) // Accost
				{
					skillsdata[1] += 4;
				}
				else if(skills[i] == 9) // Astra
				{
					skillsdata[1] += 8;
				}
				else if(skills[i] == 10) // Luna
				{
					skillsdata[1] += 16;
				}
				else if(skills[i] == 11) // Sol
				{
					skillsdata[1] += 32;
				}
				else if(skills[i] == 12) // Renewal
				{
					skillsdata[2] += 1;
				}
				else if(skills[i] == 13) // Paragon
				{
					skillsdata[2] += 2;
				}
				else if(skills[i] == 14) // Bargain
				{
					skillsdata[2] += 16;
				}
			}
			
			// Write skills to file
			for(int i=0; i<3; i++)
			{
				char writebuffer = skillsdata[i];
				database.seekp(baseposition+(offset*characteroffset)+bonusoffset+skilloffset+i);
				database.write(&writebuffer, sizeof(writebuffer));
			}
		}
	}
	return(0);
}
