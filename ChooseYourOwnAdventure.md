//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

import java.io.PrintStream;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.Scanner;
import javax.sound.sampled.AudioFormat;
import javax.sound.sampled.AudioSystem;
import javax.sound.sampled.SourceDataLine;

public class Main {
    static final boolean COLOR = true;
    static final boolean SOUND = true;
    static final double VOLUME = 0.8;
    static final String RESET = "\u001b[0m";
    static final String BOLD = "\u001b[1m";
    static final String RED = "\u001b[31m";
    static final String GREEN = "\u001b[32m";
    static final String YELLOW = "\u001b[33m";
    static final String MAGENTA = "\u001b[35m";
    static final String CYAN = "\u001b[36m";
    static final String GRAY = "\u001b[90m";
    static boolean crystalLens = false;
    static boolean lightJournal = false;
    static boolean symbolJournal = false;
    static boolean map = false;
    static boolean lantern = false;
    static boolean key = false;
    static boolean sword = false;
    static boolean swordBook = false;
    static boolean toolbox = false;
    static int loops = 0;
    static int boatRepair = 0;
    static Scanner input;
    static List<String> validChoices;
    static final float SAMPLE_RATE = 22050.0F;
    static final Random rng;

    public Main() {
    }

    public static void main(String[] args) {
        play(join(sndHorn(), sndWaves()));
        artTitle();
        System.out.println("For weeks, sailors have shared stories about a forgotten island hidden beyond a veil of fog. \nShips that pass too close report seeing a lighthouse flash briefly in the distance, \ndespite records claiming the beacon has been dark for decades.\n\nDriven by curiosity, you set sail in your small boat to uncover the truth. \nA violent storm carries your small boat off course during the night, knocking you out, \nand when dawn finally breaks, you find yourself stranded upon the very island from the legends.\n\nThe harbor appears abandoned, the village silent, and an uneasy stillness hangs in the air.\nStrange symbols, ancient ruins, and whispers of a missing lighthouse beacon \nsuggest that the island hides secrets long buried by time.\n\nDetermined to discover what happened here, you step onto the dock and begin your search.\n\nYour adventure begins...\n");
        pause(1000);
        harbor();
    }

    static void pause(int ms) {
        try {
            Thread.sleep((long)ms);
        } catch (InterruptedException var2) {
            Thread.currentThread().interrupt();
        }

    }

    static String center(String text, int width) {
        int pad = Math.max(0, (width - text.length()) / 2);
        String left = " ".repeat(pad);
        String right = " ".repeat(Math.max(0, width - text.length() - pad));
        return left + text + right;
    }

    static void title(String name) {
        play(sndForLocation(name));
        String line = "=".repeat(50);
        System.out.println();
        System.out.println("\u001b[33m\u001b[1m" + line);
        System.out.println(center(name.toUpperCase(), 50));
        System.out.println(line + "\u001b[0m");
    }

    static void art(String color, String... lines) {
        System.out.println();

        for(String l : lines) {
            System.out.println(color + l + "\u001b[0m");
        }

        System.out.println();
    }

    static void options(String list) {
        validChoices.clear();

        for(String part : list.split("[|/]")) {
            String p = part.trim();
            if (!p.isEmpty()) {
                validChoices.add(p);
            }
        }

        System.out.println("\u001b[32mYou can go:\u001b[0m");
        System.out.println("\u001b[32m\u001b[1m  " + list + "\u001b[0m");
    }

    static void yesNo() {
        validChoices.clear();
        validChoices.add("yes");
        validChoices.add("no");
        System.out.println("\u001b[32m\u001b[1m  yes | no\u001b[0m");
    }

    static String prompt() {
        System.out.print("\u001b[32m> \u001b[0m");
        String text = input.nextLine().toLowerCase().trim();
        String result = closestMatch(text);
        if (validChoices.contains(result)) {
            play(sndClick());
        } else {
            play(sndError());
        }

        return result;
    }

    static String closestMatch(String text) {
        if (text.length() >= 2 && !validChoices.contains(text)) {
            String best = null;
            int bestDist = Integer.MAX_VALUE;
            boolean tie = false;

            for(String c : validChoices) {
                int d = distance(text, c);
                if (d < bestDist) {
                    best = c;
                    bestDist = d;
                    tie = false;
                } else if (d == bestDist && !c.equals(best)) {
                    tie = true;
                }
            }

            if (best != null && !tie && bestDist <= Math.max(1, best.length() / 3)) {
                System.out.println("\u001b[90m(interpreting as \"" + best + "\")\u001b[0m");
                return best;
            } else {
                return text;
            }
        } else {
            return text;
        }
    }

    static int distance(String a, String b) {
        int[][] dp = new int[a.length() + 1][b.length() + 1];

        for(int i = 0; i <= a.length(); dp[i][0] = i++) {
        }

        for(int j = 0; j <= b.length(); dp[0][j] = j++) {
        }

        for(int i = 1; i <= a.length(); ++i) {
            for(int j = 1; j <= b.length(); ++j) {
                int cost = a.charAt(i - 1) == b.charAt(j - 1) ? 0 : 1;
                dp[i][j] = Math.min(Math.min(dp[i - 1][j] + 1, dp[i][j - 1] + 1), dp[i - 1][j - 1] + cost);
            }
        }

        return dp[a.length()][b.length()];
    }

    static void gotItem(String text) {
        play(sndPickup());
        System.out.println("\u001b[35m\u001b[1m** " + text + " **\u001b[0m\n");
    }

    static void printBar(int percent) {
        int filled = percent / 5;
        PrintStream var10000 = System.out;
        String var10001 = "#".repeat(filled);
        var10000.println("\u001b[36m[" + var10001 + "-".repeat(20 - filled) + "] " + percent + "%\u001b[0m");
    }

    static void showEnding(int num, String name, String type) {
        pause(1000);
        String color;
        if (type.equals("good")) {
            color = "\u001b[32m";
            artBeaconLit();
        } else if (type.equals("sea")) {
            color = "\u001b[31m";
            artWaves();
        } else if (type.equals("bad")) {
            color = "\u001b[31m";
            artSkull();
        } else {
            color = "\u001b[36m";
            artFog();
        }

        String line = "+" + "=".repeat(46) + "+";
        System.out.println(color + "\u001b[1m" + line);
        PrintStream var10000 = System.out;
        String var10001 = "ENDING " + num + " OF 17";
        var10000.println("|" + center(var10001, 46) + "|");
        System.out.println("|" + center(name, 46) + "|");
        System.out.println(line + "\u001b[0m");
        playAndWait(endingSound(num, type));
        System.exit(0);
    }

    public static void harbor() {
        title("The Harbor");
        if (loops < 8) {
            artHarbor(0);
        } else if (loops < 16) {
            artHarbor(1);
        } else {
            artHarbor(2);
        }

        if (loops < 4) {
            System.out.println("The harbor is silent beneath a blanket of fog.\nRotten fishing boats sway against the docks, their ropes creaking in the wind.\nTo the north lies the abandoned village, where empty streets sit untouched.\nA narrow path disappears east into a dark forest filled with towering trees.\nAtop the western cliffs stands an old lighthouse whose beacon flickers once before disappearing.");
        } else if (loops < 8) {
            System.out.print("You find yourself standing at the harbor once again.\nThe same fog rolls across the water. The same boats creak against the docks. \nYou could swear nothing has changed since your last visit.\nFor the first time, a troubling thought crosses your mind.\n");
            if (!toolbox) {
                System.out.print("But this time you noticed something new, there is a tool box sitting on the pier, \nmaybe you could repair your boat.\n");
            } else {
                System.out.print("You can try to fix the boat, but it may take a while.\n");
            }

            System.out.println("Have you been here before... or have you never really left?");
        } else if (loops < 12) {
            System.out.print("The harbor greets you with the same silence as always.\nThe ropes creak at exactly the same moments they did before. Waves strike the docks with an unnatural rhythm, repeating sounds you've begun to memorize.\nThe island feels less like a place and more like a memory trapped in a loop.\n");
            if (!toolbox) {
                System.out.print("There is a tool box sitting on the pier, maybe you could repair your boat.\n");
            } else {
                System.out.print("You can try to fix the boat, but it may take a while.\n");
            }

            System.out.println("You can no longer remember how many paths you've explored.");
        } else if (loops < 16) {
            System.out.print("As you step onto the docks, unease settles over you.\nThe fog appears frozen in place. A gull hangs in the sky without moving its wings. Even the water seems to have stopped flowing.\nSomewhere in the distance, the lighthouse flashes once.\nIt should not be able to do that.\n");
            if (!toolbox) {
                System.out.println("There is a tool box sitting on the pier, maybe you could repair your boat.");
            } else {
                System.out.println("You can try to fix the boat, but it may take a while.");
            }
        } else if (loops < 20) {
            System.out.print("The harbor no longer feels abandoned.\nShadows stand at the ends of the docks, watching from beyond the fog. Whenever you look directly at them, they disappear.\nEvery road on the island seems familiar now, yet none of them ever lead anywhere new.\nYou begin to suspect the island does not want you to leave.\n");
            if (!toolbox) {
                System.out.print("But, this time you noticed something new, there is a tool box sitting on the pier, \nmaybe you could repair your boat.\n");
            } else {
                System.out.print("You can try to fix the boat, but it may take a while.\n");
            }
        } else if (loops < 24) {
            System.out.print("You return to the harbor once more.\nThe fog parts just enough for you to see dozens of footprints along the docks. \nThey are your footprints. \nSome are old, some are fresh, and all of them lead back to this very spot.\nYou give up, and the island consumes you.\n");
            showEnding(4, "Lost in the Mist", "neutral");
        }

        String menu = "village/north | forest/east | lighthouse/west | inventory";
        if (!toolbox && loops >= 4) {
            menu = menu + " | toolbox";
        } else if (toolbox) {
            menu = menu + " | repair";
        }

        options(menu);
        String choice = prompt();
        if (!choice.equals("village") && !choice.equals("north")) {
            if (!choice.equals("forest") && !choice.equals("east")) {
                if (!choice.equals("lighthouse") && !choice.equals("west")) {
                    if (choice.equals("toolbox") && !toolbox && loops >= 4) {
                        artToolbox();
                        play(sndPickup());
                        System.out.println("You pick up the toolbox, so now you can fix the boat.\n");
                        toolbox = true;
                        pause(1000);
                        harbor();
                    } else if (choice.equals("repair") && toolbox) {
                        play(sndHammer());
                        boatRepair += 5;
                        System.out.println("The boat is " + boatRepair + "% fixed.");
                        printBar(boatRepair);
                        pause(1000);
                        if (boatRepair == 100) {
                            int randomEnd = (int)Math.floor(Math.random() * (double)3.0F);
                            if (randomEnd == 0) {
                                System.out.println("As the sun rises, you push away from the harbor and set sail through the fog. \nFor a moment, the island remains visible behind you, silent and mysterious as ever.\nThe fog gradually parts, and open water stretches before you.\nThough many of the island's secrets remain unsolved, you have escaped with your life and a story few would ever believe.\n");
                                showEnding(9, "Safe Passage", "good");
                            } else if (randomEnd == 1) {
                                System.out.println("You set sail. As the fog closes in behind you, the island fades into the distance. \nJust as freedom seems within reach, something massive strikes from below.\nYour boat is torn apart in an instant, and you disappear beneath the waves.\n");
                                showEnding(8, "Lost at Sea", "sea");
                            } else {
                                System.out.println("After repairing your boat, you finally escape the island.\nThe fog clears, the sea calms, and before long the island is nothing more than a memory.\nMonths later, strange things begin happening. Fog gathers where it shouldn't. Distant lights flicker in the darkness. \nSometimes, you hear waves crashing when no ocean is nearby.\nOne morning, you find wet footprints leading to your front door.\nThey stop there.\n");
                                showEnding(10, "The Curse Followed", "neutral");
                            }
                        }

                        harbor();
                    } else if (choice.equals("inventory")) {
                        inventory();
                        harbor();
                    } else {
                        System.out.println("Huh?");
                        pause(1000);
                        harbor();
                    }
                } else {
                    ++loops;
                    lighthouse();
                }
            } else {
                ++loops;
                forest();
            }
        } else {
            ++loops;
            village();
        }

    }

    public static void village() {
        title("The Village");
        artVillage();
        System.out.println("The village is deserted.\nEmpty market stalls line the streets and broken windows stare down like hollow eyes.\nAn old inn sits near the square, its dusty interior visible through cracked glass.\nThe town hall still stands at the end of the road, covered in faded notices.\nAn ancient fountain covered with strange carvings bubbles quietly at the center of town.\nBeyond the buildings, a beaten path leads toward the harbor.");
        options("harbor/south | inn | townhall | fountain | inventory");
        String choice = prompt();
        if (choice.equals("inn")) {
            inn();
        } else if (choice.equals("townhall")) {
            townhall();
        } else if (choice.equals("fountain")) {
            fountain();
        } else if (!choice.equals("harbor") && !choice.equals("south")) {
            if (choice.equals("inventory")) {
                inventory();
                village();
            } else {
                System.out.println("Huh?");
                pause(1000);
                village();
            }
        } else {
            harbor();
        }

    }

    public static void inn() {
        title("The Inn");
        artInn();
        System.out.println("Dust blankets every table in the inn.\nThe fireplace has long since gone cold, and abandoned belongings remain scattered throughout the rooms.\nA faded journal rests on the counter near the entrance.\nSeveral doors lead to guest rooms upstairs.\nA trapdoor behind the counter appears to lead to a cellar below.\nOutside the front door lies the village square.");
        String menu = "village | room | cellar | inventory";
        if (!lightJournal) {
            menu = menu + " | journal";
        }

        options(menu);
        String choice = prompt();
        if (choice.equals("village")) {
            village();
        } else if (choice.equals("journal") && !lightJournal) {
            artJournal();
            System.out.println("You approach the counter and pick up the journal.\nIt contains vast amounts of notes and instructions on how to run the lighthouse.");
            gotItem("This may be useful. You pocket the Lighthouse Journal");
            lightJournal = true;
            pause(1000);
            inn();
        } else if (!choice.equals("room")) {
            if (!choice.equals("cellar")) {
                if (choice.equals("inventory")) {
                    inventory();
                    inn();
                } else {
                    System.out.println("Huh?");
                    pause(1000);
                    inn();
                }
            } else {
                while(true) {
                    title("The Cellar");
                    artRowboat();
                    System.out.println("The cellar is damp and filled with abandoned crates. \nBehind a stack of barrels, you discover a tunnel leading to a small cave along the shoreline.\nAn old rowboat is tied inside the cave, somehow protected from the years of neglect.\nDo you want to use the boat to leave the island?");
                    yesNo();
                    choice = prompt();
                    if (choice.equals("no")) {
                        inn();
                        break;
                    }

                    if (choice.equals("yes")) {
                        System.out.println("You set sail from the island, leaving its mysteries behind.\nbut as you get further and further from the island, the wave progressively grew larger\nuntil the waves were larger than your boat, and they swallowed you whole.\n");
                        showEnding(3, "Swallowed by the Ocean", "sea");
                    } else {
                        System.out.println("huh?");
                        pause(1000);
                    }
                }
            }
        } else {
            while(true) {
                title("Guest Room");
                artRoom();
                System.out.println("The guest room appears untouched for years.\nA layer of dust covers the bed and furniture.\nA traveler's bag sits beside a broken window.\nThe inn waits downstairs, and the contents of the bag may hold useful clues.");
                options("inn | bag");
                choice = prompt();
                if (choice.equals("bag")) {
                    if (crystalLens) {
                        System.out.println("The bag is empty.");
                    } else {
                        artLens();
                        System.out.println("The bag contains a Crystal Lens.");
                        gotItem("You pick it up and pocket it.");
                        crystalLens = true;
                        pause(1000);
                    }
                    break;
                }

                if (choice.equals("inn")) {
                    System.out.println("You exit the room");
                    break;
                }

                System.out.println("huh?");
                pause(1000);
            }

            inn();
        }

    }

    public static void townhall() {
        title("The Town Hall");
        artTownhall();
        System.out.println("Maps and notices cover the walls of the town hall.\nMost are too damaged to read, but one map appears to mark something important deep within the forest.\nRows of shelves filled with records stretch into a small archive room.\nA wooden door near the back leads to the mayor's old office.\nSunlight shines through the front windows overlooking the village square.");
        options("village | archives | office | inventory");
        String choice = prompt();
        if (choice.equals("village")) {
            village();
        } else if (choice.equals("inventory")) {
            inventory();
            townhall();
        } else if (!choice.equals("archives")) {
            if (choice.equals("office")) {
                title("The Mayor's Office");
                if (!lantern) {
                    artLantern();
                    System.out.println("The office is left with various things still on the table.\nAlmost as if they left in a hurry and never returned.\nYou notice a lantern sitting on the table.\nIt may come in handy.");
                    gotItem("You pick up the lantern.");
                } else {
                    System.out.println("The office is left with various things still on the table.\nAlmost as if they left in a hurry and never returned.");
                }

                lantern = true;
                pause(1000);
                townhall();
            } else {
                System.out.println("Huh?");
                pause(1000);
                townhall();
            }
        } else {
            while(true) {
                title("The Archives");
                artArchives();
                if (!map) {
                    System.out.println("The archives are lined with dusty shelves filled with forgotten records.\nMost documents have faded with age, but a few still remain readable.\nA marked map of the island hangs on one wall.\nNearby, a collection of reports describes strange events that occurred before the village was abandoned.");
                    options("townhall | inspectmap | searchrecords");
                    choice = prompt();
                    if (choice.equals("inspectmap")) {
                        artMap();
                        System.out.println("The map is covered with handwritten notes and faded markings.\nSeveral locations across the island have been circled, \nincluding two on the lighthouse, the village fountain, the forest ruins, and a section of the deep forest.\nIn one corner, the mayor scribbled a final warning:\n\"The beacon must not remain dark.\"\nYou carefully remove the map from the wall and take it with you.");
                        gotItem("You obtained the Mayors Map.");
                        map = true;
                        pause(1000);
                        break;
                    }

                    if (choice.equals("searchrecords")) {
                        System.out.println("You search through stacks of dusty records and weathered journals.\nMost describe ordinary village life, but several entries \nmention a growing fog and strange lights near the lighthouse.\n");
                        if (!symbolJournal) {
                            artJournal();
                            System.out.println("One journal appears to have belonged to a person \nstudying the symbols spread across the island.\nIts pages contain notes about ancient symbols, a missing crystal lens, \nand warnings about keeping the beacon lit.\nYou take the journal with you.");
                            gotItem("You obtained the Symbol Journal.");
                        }

                        symbolJournal = true;
                        pause(1000);
                        break;
                    }

                    if (choice.equals("townhall")) {
                        break;
                    }

                    System.out.println("huh?");
                    pause(1000);
                } else {
                    System.out.println("The archives are lined with dusty shelves filled with forgotten records.\nMost documents have faded with age, but a few still remain readable.\nNearby, a collection of reports describes strange events that occurred before the village was abandoned.");
                    options("townhall | searchrecords");
                    choice = prompt();
                    if (choice.equals("searchrecords")) {
                        System.out.println("You search through stacks of dusty records and weathered journals.\nMost describe ordinary village life, but several entries \nmention a growing fog and strange lights near the lighthouse.\n");
                        if (!symbolJournal) {
                            artJournal();
                            System.out.println("One journal appears to have belonged to a person \nstudying the symbols spread across the island.\nIts pages contain notes about ancient symbols, a missing crystal lens, \nand warnings about keeping the beacon lit.\nYou take the journal with you.");
                            gotItem("You obtained the Old Journal.");
                        }

                        symbolJournal = true;
                        pause(1000);
                        break;
                    }

                    if (choice.equals("townhall")) {
                        break;
                    }

                    System.out.println("huh?");
                    pause(1000);
                }
            }

            townhall();
        }

    }

    public static void fountain() {
        title("The Fountain");
        artFountain();
        System.out.println("Crystal clear water still flows through the fountain despite years of neglect.\nSymbols carved around the stone edge seem older than the village itself.");
        if (!key) {
            System.out.println("Something shiny can be seen beneath the water.");
        }

        System.out.println("Several carvings appear detailed enough to be examined closely.\nThe surrounding village square stretches out in every direction.");
        options("village | inspectwater | readsymbols | inventory");
        String choice = prompt();
        if (choice.equals("village")) {
            village();
        } else if (choice.equals("inventory")) {
            inventory();
            fountain();
        } else if (choice.equals("readsymbols")) {
            if (!symbolJournal) {
                System.out.println("The symbols make no sense. They look like scribbles on the stone.");
            } else {
                play(sndMystic());
                System.out.println("\u001b[36mThe journal allows you to decipher the ancient writing.\n\"We built no walls.\nWe forged no chains.\nThe sea cannot be bound.\nOnly the light endures.\nShould the beacon darken,\nDo not listen to the voices.\nDo not follow the lights.\nDo not look into the water.\nThe Watcher sees through all three.\"\nThe final symbols are badly damaged, but one sentence remains:\n\"Keep the beacon alive.\"\n\u001b[0m");
            }

            fountain();
        } else if (choice.equals("inspectwater")) {
            if (!key) {
                artKey();
                System.out.println("A faint glimmer catches your eye beneath the water.\nYou reach in and recover an old silver key marked with a mysterious symbol.");
                gotItem("You obtained the Silver Key.");
            } else {
                System.out.println("The fountain's water remains clear and still.\nYou search the basin again, but whatever was hidden beneath the water is gone. \nOnly the ancient carvings remain.");
            }

            key = true;
            pause(1000);
            fountain();
        } else {
            System.out.println("Huh?");
            pause(1000);
            fountain();
        }

    }

    public static void forest() {
        title("The Forest");
        artForest();
        System.out.println("Towering trees block out most of the sunlight, leaving the forest dim and quiet.\nA rocky cave entrance can be seen between the trees to the north.\nFarther ahead, crumbling ruins emerge from the undergrowth.\nA darker trail disappears into the deep forest, where few would dare to travel.\nThrough a small gap in the trees, the harbor can still be seen.");
        options("harbor/west | cave | ruins | deepforest | inventory");
        String choice = prompt();
        if (!choice.equals("harbor") && !choice.equals("west")) {
            if (choice.equals("inventory")) {
                inventory();
                forest();
            } else if (choice.equals("cave")) {
                cave();
            } else if (choice.equals("ruins")) {
                ruins();
            } else if (choice.equals("deepforest")) {
                deepforest();
            } else {
                System.out.println("Huh?");
                pause(1000);
                forest();
            }
        } else {
            harbor();
        }

    }

    public static void cave() {
        title("The Cave");
        artCave();
        System.out.println("A massive cave entrance opens between jagged rocks.\nCold air flows out from the darkness, carrying a strange metallic smell.\nA narrow tunnel descends deeper underground.\nA larger chamber can be seen farther inside, illuminated by faint reflections on the walls.\nBehind you, the safety of the forest remains only a short walk away.");
        options("forest | tunnel | chamber | inventory");
        String choice = prompt();
        if (choice.equals("forest")) {
            forest();
        } else if (choice.equals("inventory")) {
            inventory();
            cave();
        } else if (choice.equals("tunnel") && lantern) {
            if (sword) {
                System.out.println("Holding your lantern high, you follow the tunnel deeper into the cave.\nA wolf emerges from the darkness, but with the Ancient Sword, you are able to defeat it and continue onward.\nAt the end of the tunnel, you discover a chamber overflowing with gold. \nAs you step forward to examine the treasure, your foot presses against a hidden stone plate.\nWith a thunderous crash, the tunnel entrance seals shut behind you.\nYou rush toward the exit, but massive slabs of stone have already blocked the way. There is no escape.\nSurrounded by enough treasure to last a hundred lifetimes, you realize you will never leave this chamber.\n");
                showEnding(14, "Buried Riches", "neutral");
            } else {
                System.out.println("You follow the tunnel deeper into the cave, leaving the faint light behind.\nThe passage grows narrower as strange scratches begin appearing along the stone walls. \nA low growl echoes through the darkness, followed by the sound of heavy footsteps.\nBefore you can react, a massive wolf bursts from the shadows.\nWith no weapon to defend yourself, you are quickly overwhelmed. \nThe last thing you hear is the animal's fierce snarl echoing through the tunnel.\nThe secrets of the island die with you.\n");
                showEnding(7, "Wolf in the Dark", "bad");
            }
        } else if (choice.equals("chamber") && lantern) {
            title("The Crystal Chamber");
            artChest();
            System.out.println("Holding your lantern high, you enter the chamber.\nIts walls sparkle with veins of crystal that reflect the lantern's glow throughout the cavern. \nAt the center of the room rests an ancient wooden chest reinforced with rusted iron bands.\n");
            if (!key) {
                System.out.println("You try to open the chest, but it is securely locked.\nThe key to this chest must be somewhere on the island.");
            } else {
                label61: {
                    while(!sword) {
                        System.out.println("You pull the silver key out of your pocket.\nTry and use it on the chest?");
                        yesNo();
                        choice = prompt();
                        if (choice.equals("yes")) {
                            artSword();
                            play(join(sndUnlock(), silence(150), sndSword()));
                            System.out.println("You insert the Silver Key into the lock and hear a loud click.\nThe lid slowly creaks open.\nInside lies a finely crafted sword. Though centuries old, its blade remains perfectly polished, \nand strange symbols are etched along its length.\nA faint blue light travels across the markings as you lift the weapon from the chest.");
                            gotItem("You obtained the Ancient Sword.");
                            sword = true;
                            pause(1000);
                            break label61;
                        }

                        if (choice.equals("no")) {
                            break label61;
                        }

                        System.out.println("huh?");
                        pause(1000);
                    }

                    System.out.println("The chest is empty.");
                }
            }

            cave();
        } else if (choice.equals("chamber")) {
            System.out.println("You step toward the chamber, but the darkness beyond is nearly absolute.\nNo light reaches farther than a few feet past the entrance, and the floor disappears into blackness. \nEntering without a source of light would be far too dangerous.\nYou decide to turn back.");
            cave();
        } else if (choice.equals("tunnel")) {
            System.out.println("Its too dark to enter the tunnel.");
            cave();
        } else {
            System.out.println("Huh?");
            pause(1000);
            cave();
        }

    }

    public static void ruins() {
        title("The Ruins");
        artRuins();
        System.out.println("Crumbling stone pillars rise from a clearing in the woods.\nWhatever civilization built these ruins disappeared centuries ago.\nAn open courtyard lies at the center of the site, overgrown with vines.\nNear one wall stands a large stone pillar marked with strange symbols.\nThe forest surrounds the ruins on all sides.");
        options("forest | symbols | inventory");
        String choice = prompt();
        if (choice.equals("forest")) {
            forest();
        } else if (choice.equals("inventory")) {
            inventory();
            ruins();
        } else if (choice.equals("symbols")) {
            if (symbolJournal) {
                play(sndMystic());
                System.out.println("You compare the symbols to translations in the Old Journal.\nMany sections have crumbled away, but enough remains to understand the message:\n\u001b[36m\"...we found it sleeping beneath the dark waters...\"\n\"...its hunger could not be slain, only held back...\"\n\"...from the heart of the crystal came a light of great power...\"\n\"...while the beacon burns, it shall remain imprisoned...\"\n\u001b[0mMost of the final lines have been lost to time.");
            } else {
                System.out.println("You examine the strange symbols carved into the stone.\nThe markings do not resemble any language you recognize. \nDespite your best effort, their meaning remains a mystery.\nYou leave the ruin with more questions than answers.");
            }

            ruins();
        } else {
            System.out.println("Huh?");
            pause(1000);
            ruins();
        }

    }

    public static void deepforest() {
        title("The Deep Forest");
        artDeepForest();
        System.out.println("The deeper you travel, the quieter the forest becomes.\nNo birds sing here, and no wind stirs the leaves.\nBeyond it, a rarely traveled path leads further into the darkening woods.\nIt may be wise to use a lantern before going deeper.\nGlowing eyes occasionally appear among the shadows before vanishing again.");
        options("forest | deeper | inventory");
        String choice = prompt();
        if (choice.equals("forest")) {
            forest();
        } else if (choice.equals("inventory")) {
            inventory();
            deepforest();
        } else if (choice.equals("deeper")) {
            if (map && lantern) {
                artCarvedStone();
                play(sndMystic());
                System.out.println("Guided by the Mayor's Map and the glow of your lantern, \nyou follow a nearly invisible trail through the deepest part of the forest.\nThe trees grow taller with every step, blocking out what little sunlight remains. \nEventually, the path ends at a massive stone covered in ancient symbols.\n");
                if (symbolJournal) {
                    System.out.println("Using the Old Journal, you carefully translate the weathered inscription:\n\u001b[36m\"...when the crystal gathers the light of the heavens, fragments of its power escape into the night sky...\"\n\"...their glow shall be seen near the tower, where the beacon was first raised...\"\n\"...let none mistake these lights for a warning, for they are merely echoes of the crystal's power...\"\n\u001b[0mThe remaining text has faded beyond recognition.\n");
                } else {
                    System.out.println("You cannot decipher the inscription.\n");
                }

                System.out.println("You follow the path back to the edge of the deep forest.");
                pause(1000);
                deepforest();
            } else if (!lantern) {
                System.out.println("You progress further into the deep woods with no light,\nand before you realize it, you are covered in darkness and completely lost to the island.\n");
                showEnding(8, "Engulfed by Darkness", "bad");
            } else if (!map) {
                System.out.println("You progress further into the deep woods with no idea where you are going, \nand before you know it, you are off the beaten path, and completely lost to the island.\n");
                showEnding(5, "Lost in the Woods", "neutral");
            }
        } else {
            System.out.println("Huh?");
            pause(1000);
            deepforest();
        }

    }

    public static void lighthouse() {
        title("The Lighthouse");
        artLighthouse();
        System.out.println("The lighthouse rises high above the crashing waves.\nA spiral staircase winds upward toward the lantern room near the top.\nA rusted door leads down into a basement filled with old machinery.\nOutside, a stone balcony circles the tower and overlooks the island.\nFrom here you can see the abandoned harbor docks far down below.");
        options("harbor/east | stairs | basement | balcony | inventory");
        String choice = prompt();
        if (!choice.equals("harbor") && !choice.equals("east")) {
            if (choice.equals("inventory")) {
                inventory();
                lighthouse();
            } else if (choice.equals("stairs")) {
                stairs();
            } else if (choice.equals("basement")) {
                basement();
            } else if (choice.equals("balcony")) {
                balcony();
            } else {
                System.out.println("Huh?");
                pause(1000);
                lighthouse();
            }
        } else {
            harbor();
        }

    }

    public static void stairs() {
        title("The Lantern Room");
        artLanternRoom();
        System.out.println("You are led to the lantern room that contains the enormous beacon responsible for guiding ships.\nThe crystal lens at its center is missing, leaving the lighthouse dark.\nStrange markings around the beacon match symbols found elsewhere on the island.\nA doorway opens back onto the balcony overlooking the barren sea.\nThe machinery below can be reached through the rest of the lighthouse tower.");
        options("balcony | lighthouse | inspect | inventory");
        String choice = prompt();
        if (choice.equals("balcony")) {
            balcony();
        } else if (choice.equals("lighthouse")) {
            lighthouse();
        } else if (choice.equals("inventory")) {
            inventory();
            stairs();
        } else if (!choice.equals("inspect")) {
            System.out.println("Huh?");
            pause(1000);
            stairs();
        } else {
            while(true) {
                System.out.println("You approach the complex machinery surrounding the beacon.\nIt seems to be missing a crystal lens.");
                if (!crystalLens || !lightJournal) {
                    if (!crystalLens || lightJournal) {
                        System.out.println("If you found one, you may be able to activate the lighthouse.\n");
                        break;
                    }

                    System.out.println("As you grab the crystal lens, you realize that you have no idea what you are doing.\nMaybe something else on this island could help you.\nDo you still want to try?");
                    yesNo();
                    choice = prompt();
                    if (choice.equals("no")) {
                        break;
                    }

                    if (choice.equals("yes")) {
                        System.out.println("You reach into the mechanism trying to insert the crystal lens \nand end up shocking yourself in the process.\n");
                        showEnding(12, "Shocking Death", "bad");
                    }
                } else {
                    System.out.println("You grab the crystal lens and journal stuffed deep in your pocket.\nPut it in the machine?");
                    yesNo();
                    choice = prompt();
                    if (choice.equals("yes")) {
                        System.out.println("You carefully place the crystal lens into the center of the lighthouse beacon. \nFor a moment, nothing happens, and the tower remains silent.\nThen the ancient machinery groans to life. Gears begin to turn beneath the lighthouse, \nand a brilliant beam of light cuts through the darkness, sweeping across the harbor, the village, and the forest beyond.\nAs the beacon shines out over the sea, the thick fog surrounding the island slowly begins to fade.\nLooking out from the balcony, you watch ships in the distance change course toward the light.\nThe island's darkness is contained once again.\n");
                        showEnding(16, "The Beacon Restored", "good");
                    } else if (choice.equals("no")) {
                        System.out.println("You stand before the beacon with the Crystal Lens in one hand and the Journal in the other.\nThe journal explains exactly how to restore the lighthouse. You know what the beacon was built to do.\nFor a moment, you consider activating it.\nThen you close the journal and walk away.\nWhatever happens to the island is no longer your concern.\n");
                        showEnding(13, "Turning Away", "neutral");
                    } else {
                        System.out.println("huh?");
                        pause(1000);
                    }
                }
            }

            stairs();
        }

    }

    public static void basement() {
        title("The Lighthouse Basement");
        artBasement();
        System.out.println("Rusted gears and machinery fill the basement beneath the lighthouse.\nMost of the equipment no longer works, though some mechanisms seem intact.");
        if (swordBook) {
            System.out.println("The hidden passage is wide open.\nA staircase leads back up to the lighthouse above.");
            options("lighthouse/stairs | hiddenpassage | inventory");
        } else {
            System.out.println("A row of shelves lines the wall.\nA staircase leads back up to the lighthouse above.");
            options("lighthouse/stairs | shelves | inventory");
        }

        String choice = prompt();
        if (!choice.equals("lighthouse") && !choice.equals("stairs")) {
            if (choice.equals("inventory")) {
                inventory();
                basement();
            } else if (choice.equals("hiddenpassage") && swordBook) {
                while(true) {
                    System.out.println("Are you sure you wish to enter?");
                    yesNo();
                    choice = prompt();
                    if (choice.equals("yes")) {
                        if (!lantern) {
                            System.out.println("Without any source of light you run your hands along the wall to guide yourself forward.\nYou go past multiple turns and twists as you run your hands across the walls.\nYou hear the crash of waves hitting the outside of the hidden room.\nIn the pitch black, your eyes spot a candle, it shouldn't be there.\nBut as you try to turn back to leave the dark, you are struck down by an unknown presence.\n");
                            showEnding(11, "Down in the Dark", "bad");
                        } else if (lantern && sword) {
                            artAltar();
                            play(sndMystic());
                            System.out.println("As you walk down the winding passageway maze with the lantern held far out in front of you,\nit feels almost as if something is close. And it's watching from afar between the turns of the underground maze.\nEventually you reach an expansive room with a single lit candle on a stone alter that is covered in symbols.\nYou hear the crash of waves hitting the outside of the hidden room.\n");
                            if (symbolJournal) {
                                System.out.println("After deciphering with the journal, the symbols read:\n\u001b[36m\"Keep the crystal lit, or the watcher will strike\".\n\u001b[0m");
                            }

                            artWatcher();
                            play(sndRoar());
                            System.out.println("Confused, you turn around to try and leave, but as your lantern swings around, \nyou catch something in the edge of your eye.\nA massive shape rises from the depths. Its body is cloaked in shadow, and its glowing eyes lock onto yours. \nThe creature from the ancient carvings, the Watcher Below, has awakened.\nThe beast lunges forward.\nDrawing the Ancient Sword, you meet its attack head-on. \nThe symbols engraved along the blade burst to life with a brilliant blue light, illuminating the chamber. \nWith each strike, the Watcher recoils as though the sword carries the same power as the crystal itself.\nThe battle shakes the ancient chamber. Stone cracks beneath your feet and waves crash against the walls. \nAt last, you drive the glowing blade into the creature's chest. The Watcher releases a deafening cry.\nIts body dissolves into mist and darkness, which are pulled back into the depths from which it came. \nMoments later, the underground sea becomes still. Silence fills the chamber.\nFor the first time in centuries, the island is free from the threat that haunted it. \nThough the lighthouse remains dark, the prison is no longer needed.\nYou climb the stairs back to the surface and watch as the fog surrounding the island slowly fades away.\n");
                            showEnding(17, "Slayer of the Watcher", "good");
                        } else if (lantern) {
                            artAltar();
                            play(sndMystic());
                            System.out.println("As you walk down the winding passageway maze with the lantern held far out in front of you,\nit feels almost as if something is close. And it's watching from afar between the turns of the underground maze.\nEventually you reach an expansive room with a single lit candle on a stone alter that is covered in symbols.\nYou hear the crash of waves hitting the outside of the hidden room.\n");
                            if (symbolJournal) {
                                System.out.println("After deciphering with the journal, the symbols read:\n\u001b[36m\"Keep the crystal lit, or the watcher will strike\".\n\u001b[0m");
                                artWatcher();
                                play(sndRoar());
                                System.out.println("Confused, you turn around to try and leave, but as your lantern swings around, \nyou catch something in the edge of your eye.\nA massive shape rises from the depths. \nIts body is cloaked in shadow, and its glowing eyes lock onto yours. \nThe creature from the ancient carvings, the Watcher Below, has awakened.\nThe beast lunges forward and kills you.\n");
                                showEnding(15, "It Was Watching You", "bad");
                            } else {
                                System.out.println("Confused, you try and leave to avoid trouble from whoever lit the candle.\nBut when you turn around you are met with...\nYou are struck down and die.\n");
                                showEnding(2, "Stalked", "bad");
                            }
                        }
                    } else {
                        if (choice.equals("no")) {
                            basement();
                            break;
                        }

                        System.out.println("huh?");
                        pause(1000);
                    }
                }
            } else if (choice.equals("shelves") && !swordBook) {
                play(sndClank());
                System.out.println("The shelves are covered in dust.\nEach book left and forgotten to time.\nYou notice a select book marked with finger prints, and reach to grab it.\nAs you pull the book out the bookshelves swing wide open, revealing a pitch black hidden passage.\nThe book contains a legend about a sword of light, able to fend off any evil or foe\nthat is hidden in the caves of the forest on this island.\n");
                swordBook = true;
                pause(1000);
                basement();
            } else {
                System.out.println("Huh?");
                pause(1000);
                basement();
            }
        } else {
            lighthouse();
        }

    }

    public static void balcony() {
        title("The Balcony");
        artBalcony();
        System.out.println("The balcony circles the top of the lighthouse.\nFrom here, the entire island is visible.\nBelow you lie the harbor docks, the empty village streets, and the endless forest canopy.\nA nearby doorway leads into the lantern room where the beacon is housed.\nFar out at sea, a dark shape moves beneath the waves.");
        options("lighthouse | lanternroom | balconyedge | inventory");
        String choice = prompt();
        if (choice.equals("lighthouse")) {
            lighthouse();
        } else if (choice.equals("inventory")) {
            inventory();
            balcony();
        } else if (choice.equals("lanternroom")) {
            stairs();
        } else if (choice.equals("balconyedge")) {
            System.out.println("As you approach the edge of the railing to look down below,\nyour foot catches on a piece of metal sticking out of the ground.\nAs you begin to trip you attempt to catch yourself, but it was already too late.\nYou fell to your death.\n");
            showEnding(1, "A Long Fall", "bad");
        } else {
            System.out.println("Huh?");
            pause(1000);
            balcony();
        }

    }

    public static void inventory() {
        play(sndRustle());
        String line = "-".repeat(30);
        System.out.println("\n\u001b[36m\u001b[1m" + line);
        System.out.println(center("INVENTORY", 30));
        System.out.println(line + "\u001b[0m");
        if (!crystalLens && !lightJournal && !symbolJournal && !map && !lantern && !key && !sword && !swordBook && !toolbox) {
            System.out.println("\u001b[90m  (empty)\u001b[0m");
        }

        if (crystalLens) {
            System.out.println("  - Crystal Lens");
        }

        if (lightJournal) {
            System.out.println("  - Lighthouse Journal");
        }

        if (symbolJournal) {
            System.out.println("  - Symbol Journal");
        }

        if (map) {
            System.out.println("  - Mayors Map");
        }

        if (lantern) {
            System.out.println("  - Lantern");
        }

        if (key) {
            System.out.println("  - Silver Key");
        }

        if (sword) {
            System.out.println("  - Ancient Sword");
        }

        if (swordBook) {
            System.out.println("  - Sword Story Book");
        }

        if (toolbox) {
            System.out.println("  - Toolbox");
        }

        System.out.println("\u001b[36m" + line + "\u001b[0m");
        System.out.println("You can input an items name to get details on it, or exit the inventory:");
        System.out.println("\u001b[32m\u001b[1m  item name | exit\u001b[0m");
        validChoices.clear();
        validChoices.add("exit");
        if (crystalLens) {
            validChoices.add("crystal lens");
        }

        if (lightJournal) {
            validChoices.add("lighthouse journal");
        }

        if (symbolJournal) {
            validChoices.add("symbol journal");
        }

        if (map) {
            validChoices.add("mayors map");
        }

        if (lantern) {
            validChoices.add("lantern");
        }

        if (key) {
            validChoices.add("silver key");
        }

        if (sword) {
            validChoices.add("ancient sword");
        }

        if (swordBook) {
            validChoices.add("sword story book");
        }

        if (toolbox) {
            validChoices.add("toolbox");
        }

        String choice = prompt();
        if (choice.equals("exit")) {
            System.out.println(" ");
            pause(1000);
        } else if (choice.equals("crystal lens") && crystalLens) {
            artLens();
            System.out.println("A flawless crystal lens that glows faintly when held to the light. \nIt appears to be for the lighthouse beacon.");
            pause(1000);
            inventory();
        } else if (choice.equals("lighthouse journal") && lightJournal) {
            artJournal();
            System.out.println("A weathered journal filled with diagrams, maintenance records, \nand instructions on how to operate the lighthouse written by the lighthouse keeper. \nIts pages suggest the beacon serves a purpose beyond guiding ships.");
            pause(1000);
            inventory();
        } else if (choice.equals("symbol journal") && symbolJournal) {
            artJournal();
            System.out.println("A collection of notes and translations documenting the island's strange symbols. \nWithout it, the ancient writings scattered across the island remain impossible to understand.");
            pause(1000);
            inventory();
        } else if (choice.equals("mayors map") && map) {
            artMap();
            System.out.println("The map is covered with handwritten notes and faded markings.\nSeveral locations across the island have been circled, \nincluding two on the lighthouse, the village fountain, the forest ruins, and a section of the deep forest.\nIn one corner, the mayor scribbled a final warning:\n\"The beacon must not remain dark.\"");
            pause(1000);
            inventory();
        } else if (choice.equals("lantern") && lantern) {
            artLantern();
            System.out.println("A sturdy oil lantern recovered from the abandoned town hall. \nIts warm glow pushes back the darkness lurking within caves and forgotten passages.");
            pause(1000);
            inventory();
        } else if (choice.equals("silver key") && key) {
            artKey();
            System.out.println("A tarnished silver key engraved with an unfamiliar symbol. \nThough aged by time, it feels strangely important.");
            pause(1000);
            inventory();
        } else if (choice.equals("ancient sword") && sword) {
            artSword();
            System.out.println("A legendary blade hidden away for centuries. Strange runes glow along its polished edge, \nas though the weapon still holds power from a forgotten age.");
            pause(1000);
            inventory();
        } else if (choice.equals("sword story book") && swordBook) {
            artJournal();
            System.out.println("A dusty book discovered beneath the lighthouse. Its pages tell the tale of a sword of light \nhidden somewhere deep on the island, capable of defeating an unspeakable evil.");
            pause(1000);
            inventory();
        } else if (choice.equals("toolbox") && toolbox) {
            artToolbox();
            System.out.println("A heavy toolbox left behind at the harbor. Filled with useful tools and \nspare parts, it may be your only chance to repair your damaged boat and escape the island.");
            pause(1000);
            inventory();
        } else {
            System.out.println("huh?");
            pause(1000);
            inventory();
        }

    }

    static byte[] tone(double f0, double f1, int ms, double vol, int wave) {
        int n = (int)((double)(22050.0F * (float)ms) / (double)1000.0F);
        byte[] out = new byte[n * 2];
        double phase = (double)0.0F;
        double lp = (double)0.0F;

        for(int i = 0; i < n; ++i) {
            double t = (double)i / (double)n;
            double f = f0 + (f1 - f0) * t;
            phase += (Math.PI * 2D) * f / (double)22050.0F;
            double s;
            if (wave == 0) {
                s = Math.sin(phase);
            } else if (wave == 1) {
                s = Math.sin(phase) >= (double)0.0F ? (double)1.0F : (double)-1.0F;
            } else if (wave == 2) {
                s = rng.nextDouble() * (double)2.0F - (double)1.0F;
            } else if (wave == 3) {
                s = (double)2.0F * (phase / (Math.PI * 2D) % (double)1.0F) - (double)1.0F;
            } else {
                double k = wave == 5 ? 0.97 : 0.9;
                double gain = wave == 5 ? (double)7.0F : (double)4.0F;
                lp = lp * k + (rng.nextDouble() * (double)2.0F - (double)1.0F) * ((double)1.0F - k);
                s = Math.max((double)-1.0F, Math.min((double)1.0F, lp * gain));
            }

            double env = Math.min((double)1.0F, (double)i / (double)110.25F) * Math.min((double)1.0F, (double)(n - i) / ((double)n * 0.4));
            if (wave == 5) {
                env *= Math.sin(Math.PI * t);
            }

            short v = (short)((int)(s * env * vol * 0.8 * (double)32767.0F));
            out[2 * i] = (byte)(v & 255);
            out[2 * i + 1] = (byte)(v >> 8 & 255);
        }

        return out;
    }

    static byte[] silence(int ms) {
        return new byte[(int)((double)(22050.0F * (float)ms) / (double)1000.0F) * 2];
    }

    static byte[] join(byte[]... parts) {
        int len = 0;

        for(byte[] p : parts) {
            len += p.length;
        }

        byte[] out = new byte[len];
        int pos = 0;

        for(byte[] p : parts) {
            System.arraycopy(p, 0, out, pos, p.length);
            pos += p.length;
        }

        return out;
    }

    static byte[] mix(byte[] a, byte[] b) {
        byte[] out = new byte[Math.max(a.length, b.length)];

        for(int i = 0; i + 1 < out.length; i += 2) {
            int x = i + 1 < a.length ? (short)(a[i + 1] << 8 | a[i] & 255) : 0;
            int y = i + 1 < b.length ? (short)(b[i + 1] << 8 | b[i] & 255) : 0;
            int s = Math.max(-32768, Math.min(32767, x + y));
            out[i] = (byte)(s & 255);
            out[i + 1] = (byte)(s >> 8 & 255);
        }

        return out;
    }

    static void play(byte[] data) {
        Thread t = new Thread(() -> playNow(data));
        t.setDaemon(true);
        t.start();
    }

    static void playAndWait(byte[] data) {
        playNow(data);
    }

    static void playNow(byte[] data) {
        try {
            AudioFormat fmt = new AudioFormat(22050.0F, 16, 1, true, false);
            SourceDataLine line = AudioSystem.getSourceDataLine(fmt);
            line.open(fmt);
            line.start();
            line.write(data, 0, data.length);
            line.drain();
            line.close();
        } catch (Exception var3) {
        }

    }

    static byte[] sndClick() {
        return tone((double)1000.0F, (double)700.0F, 30, 0.2, 0);
    }

    static byte[] sndError() {
        return join(tone((double)220.0F, (double)200.0F, 110, (double)0.25F, 1), silence(30), tone((double)160.0F, (double)140.0F, 160, (double)0.25F, 1));
    }

    static byte[] sndPickup() {
        return join(tone((double)523.0F, (double)523.0F, 80, 0.3, 0), tone((double)659.0F, (double)659.0F, 80, 0.3, 0), tone((double)784.0F, (double)784.0F, 200, 0.3, 0));
    }

    static byte[] sndRustle() {
        return join(tone((double)0.0F, (double)0.0F, 90, 0.15, 2), silence(40), tone((double)0.0F, (double)0.0F, 130, 0.12, 2));
    }

    static byte[] sndFootsteps() {
        return join(tone((double)0.0F, (double)0.0F, 70, 0.9, 4), silence(180), tone((double)0.0F, (double)0.0F, 70, 0.9, 4), silence(180), tone((double)0.0F, (double)0.0F, 70, 0.9, 4));
    }

    static byte[] sndWaves() {
        return tone((double)0.0F, (double)0.0F, 1600, (double)0.5F, 5);
    }

    static byte[] sndWind() {
        return mix(tone((double)0.0F, (double)0.0F, 1800, 0.4, 5), tone((double)500.0F, (double)560.0F, 1800, 0.04, 0));
    }

    static byte[] sndBell() {
        return mix(tone((double)660.0F, (double)660.0F, 900, 0.3, 0), tone((double)1320.0F, (double)1320.0F, 900, 0.1, 0));
    }

    static byte[] sndDrip() {
        return join(tone((double)1500.0F, (double)700.0F, 80, 0.3, 0), silence(350), tone((double)1300.0F, (double)600.0F, 80, 0.2, 0));
    }

    static byte[] sndWater() {
        return join(tone((double)1100.0F, (double)1700.0F, 60, 0.2, 0), silence(90), tone((double)900.0F, (double)1500.0F, 60, 0.2, 0), silence(110), tone((double)1300.0F, (double)1900.0F, 60, 0.2, 0), silence(80), tone((double)1000.0F, (double)1600.0F, 60, 0.15, 0));
    }

    static byte[] sndCreak() {
        return join(tone((double)180.0F, (double)260.0F, 350, 0.15, 3), tone((double)260.0F, (double)170.0F, 250, 0.12, 3));
    }

    static byte[] sndHorn() {
        return mix(tone((double)110.0F, (double)110.0F, 1000, 0.3, 3), tone((double)165.0F, (double)165.0F, 1000, 0.15, 0));
    }

    static byte[] sndHum() {
        return mix(tone((double)90.0F, (double)95.0F, 1200, 0.3, 0), tone((double)180.0F, (double)190.0F, 1200, 0.1, 0));
    }

    static byte[] sndShimmer() {
        return join(tone((double)1047.0F, (double)1047.0F, 100, 0.2, 0), tone((double)1319.0F, (double)1319.0F, 100, 0.2, 0), tone((double)1568.0F, (double)1568.0F, 100, 0.2, 0), tone((double)2093.0F, (double)2093.0F, 300, 0.2, 0));
    }

    static byte[] sndClank() {
        return join(tone((double)300.0F, (double)120.0F, 60, 0.4, 1), silence(150), tone((double)260.0F, (double)100.0F, 80, 0.4, 1), silence(200), tone((double)340.0F, (double)140.0F, 60, 0.3, 1));
    }

    static byte[] sndMystic() {
        return join(tone((double)220.0F, (double)220.0F, 300, 0.2, 0), tone((double)262.0F, (double)262.0F, 300, 0.2, 0), tone((double)330.0F, (double)330.0F, 300, 0.2, 0), tone((double)392.0F, (double)392.0F, 700, 0.2, 0));
    }

    static byte[] sndHammer() {
        return join(mix(tone((double)150.0F, (double)60.0F, 90, 0.6, 0), tone((double)0.0F, (double)0.0F, 40, 0.4, 4)), silence(120), tone((double)2200.0F, (double)2200.0F, 70, 0.15, 0));
    }

    static byte[] sndUnlock() {
        return join(tone((double)1500.0F, (double)1500.0F, 30, 0.3, 1), silence(90), tone((double)900.0F, (double)900.0F, 40, 0.3, 1), silence(120), tone((double)500.0F, (double)300.0F, 150, 0.3, 0));
    }

    static byte[] sndSword() {
        return join(tone((double)1200.0F, (double)2600.0F, 350, 0.2, 0), tone((double)2600.0F, (double)2600.0F, 300, 0.12, 0));
    }

    static byte[] sndGrowl() {
        return mix(tone((double)70.0F, (double)45.0F, 800, 0.4, 3), tone((double)0.0F, (double)0.0F, 800, 0.2, 4));
    }

    static byte[] sndRoar() {
        return mix(tone((double)80.0F, (double)35.0F, 1100, (double)0.5F, 3), tone((double)0.0F, (double)0.0F, 1100, 0.4, 4));
    }

    static byte[] sndRumble() {
        return mix(tone((double)0.0F, (double)0.0F, 1800, 0.8, 5), tone((double)40.0F, (double)35.0F, 1800, (double)0.5F, 0));
    }

    static byte[] sndZap() {
        return mix(tone((double)0.0F, (double)0.0F, 300, (double)0.5F, 2), tone((double)900.0F, (double)100.0F, 300, 0.4, 1));
    }

    static byte[] sndFall() {
        return join(tone((double)1000.0F, (double)120.0F, 1300, 0.3, 0), tone((double)0.0F, (double)0.0F, 150, 0.9, 4));
    }

    static byte[] sndFanfare() {
        return join(mix(tone((double)523.0F, (double)523.0F, 150, 0.2, 1), tone((double)523.0F, (double)523.0F, 150, (double)0.25F, 0)), mix(tone((double)659.0F, (double)659.0F, 150, 0.2, 1), tone((double)659.0F, (double)659.0F, 150, (double)0.25F, 0)), mix(tone((double)784.0F, (double)784.0F, 150, 0.2, 1), tone((double)784.0F, (double)784.0F, 150, (double)0.25F, 0)), mix(tone((double)1047.0F, (double)1047.0F, 700, 0.2, 1), tone((double)1047.0F, (double)1047.0F, 700, (double)0.25F, 0)));
    }

    static byte[] sndRestored() {
        return join(tone((double)200.0F, (double)1200.0F, 900, (double)0.25F, 0), sndFanfare());
    }

    static byte[] sndDeath() {
        return join(tone((double)392.0F, (double)392.0F, 250, 0.3, 3), tone((double)311.0F, (double)311.0F, 250, 0.3, 3), tone((double)262.0F, (double)262.0F, 250, 0.3, 3), tone((double)196.0F, (double)196.0F, 900, 0.3, 3));
    }

    static byte[] sndSeaEnd() {
        return join(tone((double)0.0F, (double)0.0F, 1200, 0.6, 5), tone((double)300.0F, (double)60.0F, 900, (double)0.25F, 0));
    }

    static byte[] sndFogEnd() {
        return mix(join(tone((double)220.0F, (double)215.0F, 1000, 0.2, 0), silence(150), tone((double)233.0F, (double)226.0F, 1400, 0.18, 0)), tone((double)0.0F, (double)0.0F, 2550, 0.3, 5));
    }

    static byte[] endingSound(int num, String type) {
        if (num == 6) {
            return sndRumble();
        } else if (num == 7) {
            return join(sndGrowl(), sndDeath());
        } else if (num == 10) {
            return sndRestored();
        } else if (num == 12) {
            return join(sndZap(), sndDeath());
        } else if (num == 17) {
            return sndFall();
        } else if (type.equals("good")) {
            return sndFanfare();
        } else if (type.equals("sea")) {
            return sndSeaEnd();
        } else {
            return type.equals("bad") ? sndDeath() : sndFogEnd();
        }
    }

    static byte[] sndForLocation(String name) {
        String n = name.toLowerCase();
        if (n.contains("harbor")) {
            return mix(sndWaves(), sndCreak());
        } else if (n.contains("village")) {
            return sndBell();
        } else if (!n.equals("the inn") && !n.contains("guest")) {
            if (n.contains("cellar")) {
                return sndDrip();
            } else if (n.contains("archives")) {
                return sndRustle();
            } else if (n.contains("fountain")) {
                return sndWater();
            } else if (n.contains("crystal chamber")) {
                return sndShimmer();
            } else if (n.contains("cave")) {
                return sndDrip();
            } else if (n.contains("deep forest")) {
                return sndHum();
            } else if (!n.contains("forest") && !n.contains("ruins")) {
                if (n.contains("basement")) {
                    return sndClank();
                } else if (n.contains("lantern room")) {
                    return sndHum();
                } else if (n.contains("lighthouse")) {
                    return sndHorn();
                } else {
                    return n.contains("balcony") ? sndWind() : sndFootsteps();
                }
            } else {
                return sndWind();
            }
        } else {
            return sndCreak();
        }
    }

    static void artTitle() {
        art("\u001b[36m", "          .        *              .         *      ", "     *         .        _                .         ", "          .           _/_\\_      *                 ", "   ~  ~   ~  ~   ~   |_[_]_|   ~   ~  ~   ~   ~    ", "  ~   ~  ~   ~   ~   /  |  \\  ~  ~   ~  ~   ~  ~   ", "   ~   ~  ~   ~  ~  |___|___|   ~  ~   ~   ~  ~    ", "  ~  ~   ~  ~   ~  /__________\\ ~   ~  ~   ~   ~   ", "     ~~~ THE ISLAND BEYOND THE FOG ~~~              ");
    }

    static void artHarbor(int stage) {
        if (stage == 0) {
            art("\u001b[90m", "        .     *          .        *       ", "   ~  ~   ~    ~  ~   ~   ~  ~    ~   ~  ", "       |\\                    |\\          ", "       | \\       ~~         | \\    ~~   ", "    ___|__\\___            ___|__\\___     ", "    \\_________/            \\_________/    ", " ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ ", "  ||    ||    ||    ||    ||    ||    ||  ");
        } else if (stage == 1) {
            art("\u001b[90m", "        .     ~ v ~      .        *       ", "   -  -   -    -  -   -   -  -    -   -  ", "       |\\                    |\\          ", "       | \\       --         | \\    --   ", "    ___|__\\___            ___|__\\___     ", "    \\_________/            \\_________/    ", " ---------------------------------------- ", "  ||    ||    ||    ||    ||    ||    ||  ");
        } else {
            art("\u001b[90m", "   .-.                              .-.   ", "   |_|     -  -   -    -  -         |_|   ", "  /| |\\                            /| |\\  ", "   | |       ___          ___       | |   ", "   | |   ___/___\\___  ___/___\\___   | |   ", "  ---------------------------------------- ", "  ||    ||    ||    ||    ||    ||    ||  ");
        }

    }

    static void artVillage() {
        art("\u001b[33m", "      /\\         /\\         /\\         ", "     /  \\       /  \\       /  \\        ", "    /____\\     /____\\     /____\\       ", "    | [x]|     |[x] |     | [] |       ", "    |____|  .  |____|  .  |____|       ", "  ==========================================");
    }

    static void artInn() {
        art("\u001b[33m", "        _______________        ", "       /_______________\\       ", "       |  I N N   [x]  |       ", "       |  ____   ___   |       ", "       | |    | |   |  |       ", "       | | [] | | O |  |       ", "       |_|____|_|___|__|       ");
    }

    static void artRoom() {
        art("\u001b[33m", "    .-----------------------.   ", "    |  ___________    ___   |   ", "    | |  ~  ~  ~  |  |bag|  |   ", "    | |___________|  |___|  |   ", "    | |_|         |_|  [/]  |   ", "    '-----------------------'   ");
    }

    static void artRowboat() {
        art("\u001b[36m", "    |O|   |O|   |O|                ", "    |_|   |_|   |_|                ", "    ~   ~   ~   ~   ~   ~   ~      ", "   \\__________________/            ", "    \\________________/             ", "  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~     ");
    }

    static void artTownhall() {
        art("\u001b[33m", "            _____(+)_____           ", "           /_____________\\          ", "           |_|_|_____|_|_|          ", "           | | |  _  | | |          ", "           |_|_| | | |_|_|          ", "          ====== |_| ======         ");
    }

    static void artArchives() {
        art("\u001b[33m", " ____________ ____________ ____________ ", "|=|=|=|=|=|=||=|=|=|=|=|=||=|=|=|=|=|=||", "|_|_|_|_|_|_||_|_|_|_|_|_||_|_|_|_|_|_||");
    }

    static void artMap() {
        art("\u001b[33m", "  .--------------------.  ", "  | ~~~  X   ~~~        |  ", "  |   o--o   (O)  [^]   |  ", "  |  ~~~     ~~~   ~~~  |  ", "  '--------------------'  ");
    }

    static void artLantern() {
        art("\u001b[33m", "      .-.      ", "    .-|_|-.    ", "    | (*) |    ", "    |_____|    ");
    }

    static void artJournal() {
        art("\u001b[33m", "    .----------.    ", "   / JOURNAL  /|    ", "  |__________| |    ", "  |  ~~~~~~  | |    ", "  |__________|/     ");
    }

    static void artLens() {
        art("\u001b[36m", "      ____       ", "     /\\  /\\      ", "    /__\\/__\\     ", "    \\  /\\  /     ", "     \\/__\\/      ");
    }

    static void artKey() {
        art("\u001b[90m", "   .--.              ", "  (    )=======#=#   ", "   '--'              ");
    }

    static void artSword() {
        art("\u001b[36m", "        /\\        ", "        ||        ", "        ||        ", "        ||        ", "   _____||_____   ", "   '-----||----'  ", "        ||        ", "       (__)       ");
    }

    static void artToolbox() {
        art("\u001b[33m", "       ___       ", "    __/   \\__    ", "   |_________|   ", "   |====O====|   ", "   |_________|   ");
    }

    static void artFountain() {
        art("\u001b[36m", "         \\  |  /          ", "      .  . \\|/ .  .       ", "      ~~~~~~|~~~~~~       ", "   .--'~~~~~~~~~~~~'--.   ", "   '-.______________.-'   ", "        |_________|       ");
    }

    static void artForest() {
        art("\u001b[32m", "     /\\       /\\        /\\      ", "    /  \\     /  \\      /  \\     ", "   /    \\   /    \\    /    \\    ", "  /______\\ /______\\  /______\\   ", "     ||       ||        ||      ");
    }

    static void artCave() {
        art("\u001b[90m", "       ___________________      ", "     /''                   ''\\   ", "    /     _______________    \\  ", "   |     /               \\    | ", "   |    |                 |   | ", "  _|____|_________________|___|_");
    }

    static void artChest() {
        art("\u001b[36m", "   /\\   *   /\\    *   /\\       ", "     ___________________        ", "    /___________________\\       ", "    |===================|       ", "    |========[ ]========|       ", "    |___________________|       ");
    }

    static void artRuins() {
        art("\u001b[90m", "     ___       ___           ___     ", "    |   |     |   |  /|     |   |    ", "    | O |     | O | / |     | O |    ", "    |   |     |   |/  |     |   |    ", "  __|___|_____|___|___|_____|___|__  ");
    }

    static void artDeepForest() {
        art("\u001b[90m", "   /\\   /\\    /\\    /\\   /\\   ", "  /||\\ /||\\  /||\\  /||\\ /||\\  ", "   ||   ||    ||    ||   ||   ", "     o  o         o  o         ");
    }

    static void artCarvedStone() {
        art("\u001b[36m", "     .---------.      ", "    / @  #  % ~ \\     ", "   |  ~  @  #  %  |   ", "   |  %  ~  @  #  |   ", "   |__@__#__%__~__|   ");
    }

    static void artLighthouse() {
        art("\u001b[33m", "       *  |  *        ", "        \\ | /         ", "         [###]        ", "         |   |        ", "         | O |        ", "         |   |        ", "         | O |        ", "        /|   |\\       ", "       /_|___|_\\      ", "  ~~~~~~~~~~~~~~~~~~~ ");
    }

    static void artLanternRoom() {
        art("\u001b[33m", "         .-'''-.          ", "        /  ___  \\         ", "       |  | _ |  |        ", "       |  |___|  |        ", "        \\_______/         ", "       ==|||||||==        ");
    }

    static void artBasement() {
        art("\u001b[90m", "     .-^-.        .-^-.    ", "    <  O  >======<  O  >   ", "     '-v-'        '-v-'    ", "   |===|  |=|  |=|  |===|  ");
    }

    static void artBalcony() {
        art("\u001b[36m", "     ~     *            ~       ", "  _______________________       ", "  |_|_|_|_|_|_|_|_|_|_|_|_      ", "  |   ~  ~  ~   ~/|~   ~        ", "  |  ~   ~   ~  ~  ~  ~   ~     ");
    }

    static void artAltar() {
        art("\u001b[90m", "          \\|/            ", "          (_)            ", "      .---------.        ", "      | @ # % ~ |        ", "   ___|_________|___     ");
    }

    static void artWatcher() {
        art("\u001b[31m", "      .-~~~~~~~-.       ", "     /  (@) (@)  \\      ", "    |    \\___/    |     ", "     \\  /|||||\\  /      ", "      '~~~~~~~~~'       ");
    }

    static void artSkull() {
        art("\u001b[31m", "     _____     ", "    /     \\    ", "   | () () |   ", "    \\  ^  /    ", "     |||||     ");
    }

    static void artWaves() {
        art("\u001b[31m", "          __                  ", "   ~~~~  /  \\  ~~~~  ~~~~     ", "  ~~~  ~/    \\~  ~~~~  ~~~    ", "   ~~~~~~~~~~~~~~~~~~~~~~     ");
    }

    static void artFog() {
        art("\u001b[36m", "   ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~    ", "  ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~   ", "   ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~    ");
    }

    static void artBeaconLit() {
        art("\u001b[32m", "     \\   |   /     ", "   -- .-'''-. --   ", "      | *** |      ", "      '-----'      ", "        | |        ", "       /___\\       ");
    }

    static {
        input = new Scanner(System.in);
        validChoices = new ArrayList();
        rng = new Random();
    }
}

