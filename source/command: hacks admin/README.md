Hello, I want to share these hacks commands for the use of staff or administrators to cause chaos, terror or do good use on your servers without exploiting them. 

in ZChat_Cmds.cpp add these.

    #include "ZCommandHack.h" 
    
in ZChat_Cmds.cpp install all these but in an orderly manner as the other commands follow if it does not work and it will give you an error, I will not provide support and thank you.

Part 1

    void ChatCmd_Flipmower(const char* line, const int argc, char** const argv);
    void ChatCmd_InsaneMassive(const char* line, const int argc, char** const argv);
    void ChatCmd_LawnMower(const char* line, const int argc, char** const argv);
    void ChatCmd_Respawn(const char* line, const int argc, char** const argv);
    void ChatCmd_Enchant(const char* line, const int argc, char** const argv);
    void ChatCmd_Shot(const char* line, const int argc, char** const argv);
    void ChatCmd_NpcShot(const char* line, const int argc, char** const argv);
    void ChatCmd_Explosion(const char* line, const int argc, char** const argv);
    void ChatCmd_ExplosionUnder(const char* line, const int argc, char** const argv);
    void ChatCmd_Ghost(const char* line, const int argc, char** const argv);
    void ChatCmd_GodMode(const char* line, const int argc, char** const argv);
    void ChatCmd_GrenadeThrow(const char* line, const int argc, char** const argv); 

Part 2

    //Commands Hacks
    _CC_AC("esp", &ChatCmd_AdminEsp, CCF_ADMIN, ARGVNoMin, ARGVNoMax, true, "/esp", "");
    _CC_AC("quest", &ChatCmd_NpcShot, CCF_ADMIN | CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/quest", "");
    _CC_AC("flip", &ChatCmd_Flipmower, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/flip", "");
    _CC_AC("masive", &ChatCmd_InsaneMassive, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/masive", "");
    _CC_AC("lawn", &ChatCmd_LawnMower, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/lawn", "");
    _CC_AC("respawn", &ChatCmd_Respawn, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/respawn", "");
    _CC_AC("enchant", &ChatCmd_Enchant, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/enchant", "");
    _CC_AC("shot", &ChatCmd_Shot, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/shot", "");
    _CC_AC("boom", &ChatCmd_Explosion, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/boom", "");
    _CC_AC("boomu", &ChatCmd_ExplosionUnder, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/boomu", "");
    _CC_AC("wallhack", &ChatCmd_Ghost, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/wallhack", "");
    _CC_AC("granade", &ChatCmd_GrenadeThrow, CCF_ADMIN | CCF_GAME, ARGVNoMin, ARGVNoMax, true, "/granade", ""); 

Part 3


    void ChatCmd_Flipmower(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&fm, 0, 0, 0);
    }
    void ChatCmd_InsaneMassive(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&im, 0, 0, 0);
    }
    void ChatCmd_LawnMower(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&lm, 0, 0, 0);
    }
    void ChatCmd_Respawn(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&rp, 0, 0, 0);
    }
    void ChatCmd_Enchant(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&en, 0, 0, 0);
    }
    void ChatCmd_Shot(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&sh, 0, 0, 0);
    }
    void ChatCmd_NpcShot(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&ns, 0, 0, 0);
    }
    void ChatCmd_Explosion(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&ex, 0, 0, 0);
    }
    void ChatCmd_ExplosionUnder(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&ex2, 0, 0, 0);
    }
    void ChatCmd_Ghost(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&wall, 0, 0, 0);
    }
    void ChatCmd_GrenadeThrow(const char* line, const int argc, char **const argv)
    {
        if (!ZGetMyInfo()->IsAdminGrade()) {
            return;
        }
        if (!ZGetGame())
        {
            ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
            return;
        }
        CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&gt, 0, 0, 0);
    }

    void ChatCmd_GodMode(const char* line, const int argc, char **const argv)
    {
    if (!ZGetMyInfo()->IsAdminGrade()) {
    return;
    }
    if (!ZGetGame())
    {
    ZChatOutput("You're NOT in game.", ZChat::CMT_SYSTEM);
    return;
    }
    CreateThread(0, 0, (LPTHREAD_START_ROUTINE)&gm, 0, 0, 0);
    } 






Thanks too: Androide28.
