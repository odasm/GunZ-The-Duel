Open(realsound\include\stdafx.h) <br>
Place <br>

	#if _MSC_VER > 1000
	#pragma once
	//<Define POINTER_64>
	#define POINTER_64 __ptr64
	#endif // _MSC_VER > 1000


Open (ZOptionInterface.cpp)

Fix: Iterator crash fix, in case resolution isn't supported

Find

    map< int, D3DDISPLAYMODE>::iterator iter = find_if( gDisplayMode.begin(), gDisplayMode.end(), value_equals<int, D3DDISPLAYMODE>(ddm));
    
Place under

			if (iter != gDisplayMode.end())
				pWidget->SetSelIndex(iter->first);
			else
				pWidget->SetSelIndex(0);
    
Open (RVisualMesh.cpp)

Find

	DEL(m_pCloth);
    
Replace

	 SAFE_DELETE_ARRAY(m_pCloth);
    
    
Open (MCommandBuilder.h)

Changed buffer size to fix shop list issues.

Find

	#define COMMAND_BUFFER_LEN	16384
    
Replace

	#define COMMAND_BUFFER_LEN	65535 
	
Open(MMatchConfig.h)

Find

	#define SERVER_CONFIG_DEFAULT_NJ_DBAGENT_IP ""
	
Replace

	#define SERVER_CONFIG_DEFAULT_NJ_DBAGENT_IP "YOUR IP"

Open(MAsyncDBJob_GetLoginInfo.cpp) <br>
It's an SQLi pretty much. It allows for remote execution of queries on the server machine. Any and all queries can be run through it as long as the user account has access.
Find

    // ¿ø·¡ °èÁ¤Àº ³Ý¸¶ºí¿¡ ÀÖÀ¸¹Ç·Î ÇØ´ç °èÁ¤ÀÌ ¾øÀ¸¸é »õ·Î »ý¼ºÇÑ´Ù. 
    if (!pDBMgr->GetLoginInfo(m_szUserID, &m_nAID, m_szDBPassword))
    {
        int nGunzSex;    // °ÇÁîµðºñÀÇ ¼ºº°°ªÀº ³Ý¸¶ºí ¼ºº°°ª°ú ¹Ý´ëÀÌ´Ù.
        if (m_nSex == 0) nGunzSex = 1; else nGunzSex = 0;

        int nCert = 0;
        if (strlen(m_szCertificate) > 0)
        {
            if (m_szCertificate[0] == '1') nCert =1;
        }

        pDBMgr->CreateAccount(m_szUserID, m_szUniqueID, nCert, m_szName, m_nAge, nGunzSex);
        pDBMgr->GetLoginInfo(m_szUserID, &m_nAID, m_szDBPassword);
    } 

Replace

    // ¿ø·¡ °èÁ¤Àº ³Ý¸¶ºí¿¡ ÀÖÀ¸¹Ç·Î ÇØ´ç °èÁ¤ÀÌ ¾øÀ¸¸é »õ·Î »ý¼ºÇÑ´Ù. 
    if (!pDBMgr->GetLoginInfo(m_szUserID, &m_nAID, m_szDBPassword))
    {
        /*int nGunzSex;    // °ÇÁîµðºñÀÇ ¼ºº°°ªÀº ³Ý¸¶ºí ¼ºº°°ª°ú ¹Ý´ëÀÌ´Ù.
        if (m_nSex == 0) nGunzSex = 1; else nGunzSex = 0;

        int nCert = 0;
        if (strlen(m_szCertificate) > 0)
        {
            if (m_szCertificate[0] == '1') nCert =1;
        }

        pDBMgr->CreateAccount(m_szUserID, m_szUniqueID, nCert, m_szName, m_nAge, nGunzSex);
        pDBMgr->GetLoginInfo(m_szUserID, &m_nAID, m_szDBPassword);*/
        
        SetResult(MASYNC_RESULT_FAILED);
        return;        
    } 

Open(ZGameInterface.cpp) <br>
Find

	char* pszAgentIP = "";

Change

	char* pszAgentIP = "YOUR IP";

Open(MPdb.cpp) <br>
Find <br>

	if (!g_pfnEnumerateLoadedModules(hProcess, EnumLoadedModulesCallback, (PVOID)hProcess))

Change

	if (!EnumerateLoadedModules(hProcess, (PENUMLOADED_MODULES_CALLBACK)EnumLoadedModulesCallback, (PVOID)hProcess))
	
Open(Gunz/stdafx.h) <br>
Add <br>

	#pragma comment(lib, "legacy_stdio_definitions.lib");


Open(ZGame.cpp) <br>
Find <br>

	void ZGame::OnPeerHPInfo(MCommand *pCommand)
	{
		MUID uid = pCommand->GetSenderUID();
		ZCharacter* pCharacter = m_CharacterManager.Find(uid);
		if (!pCharacter) return;

		float fHP=0.0f;
		pCommand->GetParameter(&fHP, 0, MPT_FLOAT);

		if(ZGetGameInterface()->GetCombatInterface()->GetObserverMode()) {
			pCharacter->SetHP(fHP);
		}
	}

Replace

	void ZGame::OnPeerHPInfo(MCommand *pCommand)
	{
		MUID uid = pCommand->GetSenderUID();
		ZCharacter* pCharacter = m_CharacterManager.Find(uid);
		if (!pCharacter) return;

		float fHP = 0.0f;
		pCommand->GetParameter(&fHP, 0, MPT_FLOAT);
		//<Lógica de HP>
		if (!IsReplay() && pCharacter->GetUID() != ZGetMyUID() && uid != ZGetMyUID())
			pCharacter->SetHP(fHP);
		else if (IsReplay() || ZGetGameInterface()->GetCombatInterface()->GetObserverMode())
			pCharacter->SetHP(fHP);
	}

Find <br>

	void ZGame::OnPeerHPAPInfo(MCommand *pCommand)
	{
		MUID uid = pCommand->GetSenderUID();
		ZCharacter* pCharacter = m_CharacterManager.Find(uid);
		if (!pCharacter) return;

		float fHP=0.0f;
		pCommand->GetParameter(&fHP, 0, MPT_FLOAT);
		float fAP=0.0f;
		pCommand->GetParameter(&fAP, 1, MPT_FLOAT);

		if(ZGetGameInterface()->GetCombatInterface()->GetObserverMode()) {
			pCharacter->SetHP(fHP);
			pCharacter->SetAP(fAP);
		}
	}
	
Change <br>

	void ZGame::OnPeerHPAPInfo(MCommand *pCommand)
	{
		MUID uid = pCommand->GetSenderUID();
		ZCharacter* pCharacter = m_CharacterManager.Find(uid);
		if (!pCharacter) return;

		float fHP = 0.0f;
		pCommand->GetParameter(&fHP, 0, MPT_FLOAT);
		float fAP = 0.0f;
		pCommand->GetParameter(&fAP, 1, MPT_FLOAT);

		//<Lógica de HP/AP>
		if (!IsReplay() && pCharacter->GetUID() != ZGetMyUID() && uid != ZGetMyUID())
		{
			pCharacter->SetHP(fHP);
			pCharacter->SetAP(fAP);
		}
		else if (IsReplay() || ZGetGameInterface()->GetCombatInterface()->GetObserverMode())
		{
			pCharacter->SetHP(fHP);
			pCharacter->SetAP(fAP);
		}
	}

Find <br>

	void ZGame::OnPeerDuelTournamentHPAPInfo(MCommand *pCommand)
	{
		MUID uid = pCommand->GetSenderUID();
		ZCharacter* pCharacter = m_CharacterManager.Find(uid);
		if (!pCharacter) return;

		BYTE MaxHP = 0;
		BYTE MaxAP = 0;
		BYTE HP = 0;
		BYTE AP = 0;

		pCommand->GetParameter(&MaxHP, 0, MPT_UCHAR);	
		pCommand->GetParameter(&MaxAP, 1, MPT_UCHAR);

		pCommand->GetParameter(&HP, 2, MPT_UCHAR);	
		pCommand->GetParameter(&AP, 3, MPT_UCHAR);

		if(ZGetGame()->GetMatch()->GetMatchType() == MMATCH_GAMETYPE_DUELTOURNAMENT) {
			((ZRuleDuelTournament*)m_Match.GetRule())->SetPlayerHpApForUI(uid, (float)MaxHP, (float)MaxAP, (float)HP, (float)AP);
		}

		if(ZGetGameInterface()->GetCombatInterface()->GetObserverMode()) {
			pCharacter->SetMaxHP((float)MaxHP);
			pCharacter->SetMaxAP((float)MaxAP);
			pCharacter->SetHP((float)HP);
			pCharacter->SetAP((float)AP);
		}
	}
	
Change <br>

	void ZGame::OnPeerDuelTournamentHPAPInfo(MCommand *pCommand)
	{
		MUID uid = pCommand->GetSenderUID();
		ZCharacter* pCharacter = m_CharacterManager.Find(uid);
		if (!pCharacter) return;

		BYTE MaxHP = 0;
		BYTE MaxAP = 0;
		BYTE HP = 0;
		BYTE AP = 0;

		pCommand->GetParameter(&MaxHP, 0, MPT_UCHAR);
		pCommand->GetParameter(&MaxAP, 1, MPT_UCHAR);

		pCommand->GetParameter(&HP, 2, MPT_UCHAR);
		pCommand->GetParameter(&AP, 3, MPT_UCHAR);

		if (ZGetGame()->GetMatch()->GetMatchType() == MMATCH_GAMETYPE_DUELTOURNAMENT) {
			((ZRuleDuelTournament*)m_Match.GetRule())->SetPlayerHpApForUI(uid, (float)MaxHP, (float)MaxAP, (float)HP, (float)AP);
		}

		//<Logic of de HP/AP>
		if (pCharacter->GetUID() != ZGetMyUID() && uid != ZGetMyUID())
		{
			pCharacter->SetMaxHP((float)MaxHP);
			pCharacter->SetMaxAP((float)MaxAP);
			pCharacter->SetHP((float)HP);
			pCharacter->SetAP((float)AP);
		}
		if (ZGetGameInterface()->GetCombatInterface()->GetObserverMode())
		{
			pCharacter->SetMaxHP((float)MaxHP);
			pCharacter->SetMaxAP((float)MaxAP);
			pCharacter->SetHP((float)HP);
			pCharacter->SetAP((float)AP);
		}
	}

Open (ZOptionInterface.cpp) <br>
Find <br>

	static int heights[]={ 480,600,768,960,1200,800,900, 1050, 1200, 1600};
	
Change <br>

	static int widths[] = { 800, 1024, 1280, 1280, 1366, 1600, 1280, 1440, 1600, 1650, 1920, 1920, 2560 };
	static int heights[] = { 600, 768, 720, 960, 768, 1200, 800, 900, 900, 1050, 1080, 1200, 1600 };

Open(RBspObject.cpp) <br>
Find <br>

	for(int j=0;j<3;j++) {
		BSPVERTEX *pv = &m_pOcVertices[indices[j]] + pNode->m_nBaseVertexIndex;
		v[j].coord = *pv->Coord();
		v[j].tu2 = pv->tu1;
		v[j].tv2 = pv->tv1;
	}
	
Change <br>

	for(int j=0;j<3;j++) {
	//<Dynamic light fix>
		BSPVERTEX *pv = &m_pOcVertices[indices[j]] + pNode->m_nBaseVertexIndex;;
		v[j].coord = *pv->Coord();
		v[j].tu2 = pv->tu1;
		v[j].tv2 = pv->tv1;
	}
	
Open(RealSpace2.h) <br>
Find <br>

	void RSetFrameLimitPerSeceond(unsigned short nFrameLimit)
	{
		g_nFrameLimitValue = nFrameLimit;
	}
	
Change <br>

	void RSetFrameLimitPerSeceond(unsigned short nFrameLimit)
	{
		switch (nFrameLimit)
		{
		case 0:	{	g_nFrameLimitValue = 0;		}	break;
		case 1:	{	g_nFrameLimitValue = 60;	}	break;
		case 2:	{	g_nFrameLimitValue = 120;	}	break;
		case 3:	{	g_nFrameLimitValue = 240;	}	break;
		default:{	g_nFrameLimitValue = 0;		}	break;
		}
	}
	
Open(RealSpace2.cpp) <br>
Find <br>

	void RAdjustWindow(const RMODEPARAMS *pModeParams)

Change <br>

	if(pModeParams->bFullScreen)
	{
		SetWindowLong( g_hWnd, GWL_STYLE, WS_POPUP | WS_SYSMENU );
	}
	else
		SetWindowLong(g_hWnd, GWL_STYLE, WS_VISIBLE | WS_OVERLAPPED | WS_BORDER | WS_CAPTION | WS_SYSMENU | WS_MAXIMIZEBOX | WS_MINIMIZEBOX);

Open(MMatchServer_Stage.cpp)<br>
Find <br>

	if (MMUG_EVENTMASTER == pObj->GetAccountInfo()->m_nUGrade) {
		OnEventChangeMaster(pObj->GetUID());
	}
	
Change <br>

	//Disable auto change master for UGrade 252
	//if (MMUG_EVENTMASTER == pObj->GetAccountInfo()->m_nUGrade) {
	//	OnEventChangeMaster(pObj->GetUID());
	//}

Open(ZCharacter.cpp) <br>
Find <br>

	float fAddedAP = DEFAULT_CHAR_AP;
	for (int i = 0; i < MMCIP_END; i++) {
		if (!m_Items.GetItem(MMatchCharItemParts(i))->IsEmpty()) {

			if(m_Items.GetItem(MMatchCharItemParts(i))->GetDesc()->m_nAP.Ref() > 40) {
				m_Items.GetItem(MMatchCharItemParts(i))->GetDesc()->m_nAP.Ref() = 0; 
			}
			fAddedAP += m_Items.GetItem(MMatchCharItemParts(i))->GetDesc()->m_nAP.Ref();
		}
	}
	
Change <br>

		float fAddedAP = DEFAULT_CHAR_AP;
		for (int i = 0; i < MMCIP_END; i++) {
			if (!m_Items.GetItem(MMatchCharItemParts(i))->IsEmpty()) {

				//Bypass AP limit of 40 (MAIET trap check)
				//if(m_Items.GetItem(MMatchCharItemParts(i))->GetDesc()->m_nAP.Ref() > 40) {
				//	m_Items.GetItem(MMatchCharItemParts(i))->GetDesc()->m_nAP.Ref() = 0; 
				//}
				fAddedAP += m_Items.GetItem(MMatchCharItemParts(i))->GetDesc()->m_nAP.Ref();
			}
		}

Open(MClient.cpp) <br>
Find <br>

		case MC_LOCAL_ECHO:
			if(pCommand->GetParameter(szMessage, 0, MPT_STR, sizeof(szMessage))==false) break;
			OutputMessage(szMessage, MZMOM_LOCALREPLY);
		break;

Change <br>

		case MC_LOCAL_ECHO:
			//Exploit fix (Fake echo from different player)
			//if(pCommand->GetParameter(szMessage, 0, MPT_STR, sizeof(szMessage))==false) break;
			//OutputMessage(szMessage, MZMOM_LOCALREPLY);
		break;

Find <br>

		case MC_NET_DISCONNECT:
			Disconnect(m_Server);
		break;
		
Change <br>

		case MC_NET_DISCONNECT:
			//Exploit fix (Fake disconnect from different player)
			if (pCommand->GetSenderUID() != GetUID())
			{
				break;
			}
			Disconnect(m_Server);
		break;

Open(MMatchServer_Agent.cpp) <br>
Find <br>

	void MMatchServer::OnRegisterAgent(const MUID& uidComm, char* szIP, int nTCPPort, int nUDPPort)
	{
	
Add under <br>

	//Fix agent invalid ip crash
	if (strstr(szIP, "%") != NULL)
	{
		return;
	}
	
Find <br>

	void MMatchServer::AgentClear()
	{
		MAgentObjectMap::iterator i = m_AgentMap.begin();
		for(;i!=m_AgentMap.end(); i++)
		{
			AgentRemove( i->first, &i);
		}
	}
	
Change <br>

	void MMatchServer::AgentClear()
	{
		MAgentObjectMap::iterator i = m_AgentMap.begin();
		//Iterator dereference fix
		for (; i != m_AgentMap.end();)
		{
			AgentRemove(i->first, &i);
		}
	}
	
Find <br>

	pChar->SetRelayPeer(true);
	LOG(LOG_DEBUG, "%s Request relay peer on %s", pChar->GetName(), pPeer->GetName());
	
Change <br>

	pChar->SetRelayPeer(true);
	//Logfix, added newline
	LOG(LOG_DEBUG, "%s Request relay peer on %s\n", pChar->GetName(), pPeer->GetName());

Open(ZScreenEffectManager.cpp) <br>
Find <br>

	void ZScreenEffectManager::DrawCombo()
	
Add under <br>

	//VMesh NULL and GetFrameInfo fix
	if (m_pComboEffects[i]->GetVMesh() == NULL) continue;
	if (m_pComboEffects[i]->GetVMesh()->GetFrameInfo(ani_mode_lower) == NULL) continue;

Open(RCamera.cpp) <br>
Find <br>

	void RSetProjection(float fFov,float fAspect,float fNearZ,float fFarZ)

Add under <br>

	FLOAT fAspect = (FLOAT)RGetScreenWidth() / (FLOAT)RGetScreenHeight();
	
Open(RealSpace2.cpp) <br>
Find <br>

	if(g_rsnRenderFlags && RRENDER_CLEAR_BACKBUFFER)
	{
		//if(g_bStencilBuffer)
			//g_pd3dDevice->Clear(0,NULL,D3DCLEAR_TARGET | D3DCLEAR_ZBUFFER |D3DCLEAR_STENCIL ,g_clear_color,1.0f ,0L);
		//else
			g_pd3dDevice->Clear(0,NULL,D3DCLEAR_TARGET | D3DCLEAR_ZBUFFER ,g_clear_color,1.0f ,0L);
	}
	else
		g_pd3dDevice->Clear(0,NULL,D3DCLEAR_ZBUFFER ,g_clear_color,1.0f ,0);
		
Change <br>

	//Replaced && to &
	if (g_rsnRenderFlags & RRENDER_CLEAR_BACKBUFFER)
	{
		g_pd3dDevice->Clear(0, NULL, D3DCLEAR_TARGET | D3DCLEAR_ZBUFFER, g_clear_color, 1.0f, 0L);
	}
	else
		g_pd3dDevice->Clear(0, NULL, D3DCLEAR_ZBUFFER, g_clear_color, 1.0f, 0);

Open(ZInterfaceListener.cpp) <br>
Find <br>

			int nPlayerCnt = (int)ZGetGameClient()->GetMatchStageSetting()->m_CharSettingList.size();
			if(nPlayerCnt > pStageSetting->nMaxPlayers && nPlayerCnt!=0 && pStageSetting->nMaxPlayers!=0)
			{
				char szText[128] = {0,};
				sprintf(szText, "%s\n", ZErrStr(MERR_PERSONNEL_TOO_MUCH));
				ZGetGameInterface()->ShowMessage(szText);
				return false;
			}

Replace <br>

			//Disabled too many players popup client-side
			int nPlayerCnt = (int)ZGetGameClient()->GetMatchStageSetting()->m_CharSettingList.size();
			if (nPlayerCnt > pStageSetting->nMaxPlayers && nPlayerCnt != 0 && pStageSetting->nMaxPlayers != 0 && (ZGetGameTypeManager()->IsQuestDerived(pStageSetting->nGameType)))
			{
				char szText[128] = { 0, };
				sprintf(szText, "%s\n", ZErrStr(MERR_PERSONNEL_TOO_MUCH));
				ZGetGameInterface()->ShowMessage(szText);
				return false;
			}

Open(ZGameInterface_OnCommand.cpp) <br>
Find <br>

	case MC_MATCH_STAGE_RESPONSE_QUICKJOIN:
		{
			if (ZApplication::GetGameInterface()->GetState() == GUNZ_LOBBY)
			{
				int nResult;
				pCommand->GetParameter(&nResult, 0, MPT_INT);
				if (nResult != MOK)
				{
					ZApplication::GetGameInterface()->ShowErrorMessage( nResult );
				}

				MUID uidStage;
				pCommand->GetParameter(&uidStage, 1, MPT_UID);

				ZPostRequestStageJoin(ZGetGameClient()->GetPlayerUID(), uidStage);
			}
		}
		break;
		
Replace <br>

	case MC_MATCH_STAGE_RESPONSE_QUICKJOIN:
		{
			if (ZApplication::GetGameInterface()->GetState() == GUNZ_LOBBY)
			{
				int nResult;
				pCommand->GetParameter(&nResult, 0, MPT_INT);
				if (nResult != MOK)
				{
					ZApplication::GetGameInterface()->ShowErrorMessage( nResult );
				}

				MUID uidStage;
				pCommand->GetParameter(&uidStage, 1, MPT_UID);

				ZPostRequestStageJoin(ZGetGameClient()->GetPlayerUID(), uidStage);
				//Fix quick join glitch (room tags)
				ZApplication::GetGameInterface()->EnableLobbyInterface(true);
			}
		}
		break;

Find <br>

	case MC_MATCH_STAGE_REQUIRE_PASSWORD:
		{
			MUID uidStage = MUID(0,0);
			char szStageName[256];
			pCommand->GetParameter(&uidStage, 0, MPT_UID);
			pCommand->GetParameter(szStageName, 1, MPT_STR, sizeof(szStageName) );

			ZRoomListBox* pRoomList;
			pRoomList = (ZRoomListBox*)ZApplication::GetGameInterface()->GetIDLResource()->FindWidget( "Lobby_StageList" );
			if ( pRoomList != NULL ) pRoomList->SetPrivateStageUID(uidStage);

			ZApplication::GetGameInterface()->ShowPrivateStageJoinFrame(szStageName);
		}
		break;

Replace <br>

	case MC_MATCH_STAGE_REQUIRE_PASSWORD:
		{
			MUID uidStage = MUID(0,0);
			char szStageName[256];
			pCommand->GetParameter(&uidStage, 0, MPT_UID);
			pCommand->GetParameter(szStageName, 1, MPT_STR, sizeof(szStageName) );

			//Event Team join fix
			ZApplication::GetGameInterface()->EnableLobbyInterface(true);

			ZRoomListBox* pRoomList;
			pRoomList = (ZRoomListBox*)ZApplication::GetGameInterface()->GetIDLResource()->FindWidget( "Lobby_StageList" );
			if ( pRoomList != NULL ) pRoomList->SetPrivateStageUID(uidStage);

			ZApplication::GetGameInterface()->ShowPrivateStageJoinFrame(szStageName);
		}
		break;


Open(ZCharacterManager.cpp) <br>
Find <br>

	void ZCharacterManager::Delete(MUID uid)
	{
		iterator itor = find(uid);

		if (itor != end()) {

			ZCharacter* pCharacter = (*itor).second;

			ZGetObjectManager()->Delete((ZObject*)pCharacter);

			ZGetGame()->m_VisualMeshMgr.Del(pCharacter->m_nVMID.Ref());
			delete pCharacter; pCharacter = NULL;
			erase(itor);
		}
	}
	
Replace <br>

	void ZCharacterManager::Delete(MUID uid)
	{
		iterator itor = find(uid);

		if (itor != end()) {

			ZCharacter* pCharacter = (ZCharacter*)(*itor).second;

			ZGetObjectManager()->Delete((ZObject*)pCharacter);

			ZGetGame()->m_VisualMeshMgr.Del(pCharacter->m_nVMID.Ref());

			//Safe delete
			SAFE_DELETE(pCharacter);

			//delete pCharacter; pCharacter = NULL;
			erase(itor);
		}
	}

Open (ZGame.cpp) <br>
Find <br>

	const char *szAnonymous = "?¾Æ¹«°³?";
	
Replace <br>

	const char* szAnonymous = "?Unknown?";

Open(ZinitialLoading.cpp) <br>
Find

			while ((RGetScreenWidth()-70) > (pFont->GetWidth(temp)))
			{
				strcat(temp, "³²");
			}
			
Replace <br>

			while ((RGetScreenWidth()-70) > (pFont->GetWidth(temp)))
			{
				//changed text
				strcat(temp, "..");//"³²");
			}

Open(MMatchRule.cpp) <br>
Find

	void MMatchRule::OnRoundEnd()
	
Replace <br>

	void MMatchRule::OnRoundEnd()
	{
		///Clearsuicide list on each new round if size > 0
		if (GetStage()->GetSuicideList().size() > 0)
			GetStage()->GetSuicideList().clear();
	}

Find <br>

		MMatchObject* pObj = (MMatchObject*)(*itor).second;
		if (pObj->GetEnterBattle() == false) continue;
		
Add <br>

		///you dont need admin info if they're hiding
		if (IsAdminGrade(pObj) && pObj->CheckPlayerFlags(MTD_PlayerFlags::MTD_PlayerFlags_AdminHide))
			continue;

Open(MMatchStage.cpp) <br>
Find <br>

	if (count == 0 && !MMATCH_GAMETYPE_DUEL)
	{
		pRelayMapList[0].nMapID = MMATCH_MAP_MANSION;
		count = 1;
	}else{
		pRelayMapList[0].nMapID = MMATCH_MAP_HALL;
		count = 1;
	}

Replace <br>

	if (count == 0)
	{
		pRelayMapList[0].nMapID = MMATCH_MAP_MANSION;
		count = 1;
	}

Open(MMatchClient.cpp)

Replace

	void MMatchClient::OnAllowTunnelingTCP()
	{
		SetAllowTunneling(true);
	}

	void MMatchClient::OnAllowTunnelingUDP()
	{
		SetAllowTunneling(true);
		SetAgentPeerFlag(true);
	}

Replace

		if (GetBridgePeerFlag() == false) {
			SendCommandByTunneling(pCommand);
		} else {
			if (pCommand->GetReceiverUID() == MUID(0,0)) {	// BroadCasting
				int nTunnelingCount = 0;

				// Peer2Peer ¸Þ¼¼Áö´Â Sender°¡ ÇÃ·¹ÀÌ¾îÀÌ´Ù.
				for (MMatchPeerInfoList::iterator itor = m_Peers.begin(); 
					itor != m_Peers.end(); ++itor)
				{
					MMatchPeerInfo* pPeerInfo = (*itor).second;
					if ( (pPeerInfo->uidChar==MUID(0,0)) || 
						 (pPeerInfo->uidChar != GetPlayerUID()) )	
					{
						if ( (pPeerInfo->GetProcess() == false) &&
							 (pPeerInfo->GetUDPTestResult() == false) )
							nTunnelingCount++;
						else
						{
							// minor fix
							if (pPeerInfo->szIP != 0 && pPeerInfo->nPort != 0)
								SendCommandByUDP(pCommand, pPeerInfo->szIP, pPeerInfo->nPort);
						}
					}
				}



Replace

				if (nTunnelingCount > 0) {
					SendCommandByTunneling(pCommand);
				}
			} else {
				MMatchPeerInfo* pPeerInfo = FindPeer(pCommand->GetReceiverUID());
				if (pPeerInfo) {
					if ( (pPeerInfo->GetProcess() == false) &&
						 (pPeerInfo->GetUDPTestResult() == false) )
						SendCommandByTunneling(pCommand);
					else	
					{
						// minor fix
						if (pPeerInfo->szIP != 0 && pPeerInfo->nPort != 0)
							SendCommandByUDP(pCommand, pPeerInfo->szIP, pPeerInfo->nPort);
					}
				}
			}
		}
	}
	else 

Replace

	void MMatchClient::SendCommandByTunneling(MCommand* pCommand)
	{
		if (GetAllowTunneling() == false) {
		} else {
			if (GetBridgePeerFlag() == false) {
				MCommand* pCmd = CreateCommand(MC_AGENT_TUNNELING_TCP, GetAgentServerUID());
					pCmd->AddParameter(new MCmdParamUID(GetPlayerUID()));
					pCmd->AddParameter(new MCmdParamUID(pCommand->GetReceiverUID()));

					// Create Param : Command Blob ////
					if (!MakeTunnelingCommandBlob(pCmd, pCommand))
					{
						delete pCmd; pCmd=NULL; return;
					}
					///////////////////////////////////
				SendCommandToAgent(pCmd);
				delete pCmd;	// PACKETQUEUE ¸¸µé¶§±îÁö delete ÀÓ½Ã·Î »ç¿ë
			} else {
				MCommand* pCmd = CreateCommand(MC_AGENT_TUNNELING_UDP, GetAgentServerUID());
					pCmd->AddParameter(new MCmdParamUID(GetPlayerUID()));
					pCmd->AddParameter(new MCmdParamUID(pCommand->GetReceiverUID()));
					// Create Param : Command Blob ////
					if (!MakeTunnelingCommandBlob(pCmd, pCommand))
					{
						delete pCmd; pCmd=NULL; return;
					}
					///////////////////////////////////
				SendCommandByUDP(pCmd, GetAgentIP(), GetAgentPeerPort());
				delete pCmd;	// PACKETQUEUE ¸¸µé¶§±îÁö delete ÀÓ½Ã·Î »ç¿ë
			}
		}
	}

Open(RealCPNet.cpp) <br>
Replace <br>

	#ifndef _PUBLISH
		char szFileName[_MAX_DIR];
		GetModuleFileName(NULL, szFileName, _MAX_DIR);
		HANDLE hProcess = MProcessController::OpenProcessHandleByFilePath(szFileName);
		//Do not crash on RealCPNet crashes	
		//TerminateProcess(hProcess, 0);
	#endif

Open(MMatchItem.cpp) <br>

Find

	if (!xmlIniData.LoadFromMemory(buffer))


Replace <br>

	if (!xmlIniData.LoadFromMemory(buffer))
	{
		//memleak fix.
		delete[] buffer;
		m_nChecksum = 0;
		xmlIniData.Destroy();
		return false;
	}
	delete[] buffer;
	mzf.Close();

Open(MMatchServer.cpp) <br>

Find <br>

	MMatchServer::ParseUDPPacket


Replace <br>

	void MMatchServer::ParseUDPPacket(char* pData, MPacketHeader* pPacketHeader, DWORD dwIP, WORD wRawPort)
	{
		switch (pPacketHeader->nMsg)
		{
		case MSGID_RAWCOMMAND:
		{
			unsigned short nCheckSum = MBuildCheckSum(pPacketHeader, pPacketHeader->nSize);
			if (pPacketHeader->nCheckSum != nCheckSum) {
				static int nLogCount = 0;
				if (nLogCount++ < 100) {	// Log Flooding ¹æÁö
					mlog("MMatchServer::ParseUDPPacket() -> CHECKSUM ERROR(R=%u/C=%u)\n",
						pPacketHeader->nCheckSum, nCheckSum);
				}
				return;
			}
			else {
				unsigned short nTotalSize = 0;
				memcpy(&nTotalSize, pData, sizeof(nTotalSize));

				MCommand* pCmd = new MCommand();
				if (!pCmd->SetData(pData, &m_CommandManager))
				{
					delete pCmd;
					mlog("fail ParseUDPPacket.\n");
					return;
				}

				if (pCmd->GetID() == MC_MATCH_BRIDGEPEER) {
					pCmd->m_Sender = MUID(0, 0);
					pCmd->m_Receiver = m_This;

					unsigned long nPort = ntohs(wRawPort);

					MCommandParameterUInt* pParamIP = (MCommandParameterUInt*)pCmd->GetParameter(1);
					MCommandParameterUInt* pParamPort = (MCommandParameterUInt*)pCmd->GetParameter(2);
					if (pParamIP == NULL || pParamIP->GetType() != MPT_UINT)
					{
						delete pCmd;
						break;
					}
					if (pParamPort == NULL || pParamPort->GetType() != MPT_UINT)
					{
						delete pCmd;
						break;
					}

					char pData[64];
					MCommandParameterUInt(dwIP).GetData(pData, 64);
					pParamIP->SetData(pData);
					MCommandParameterUInt(nPort).GetData(pData, 64);
					pParamPort->SetData(pData);

					PostSafeQueue(pCmd);
				} //Handle default packets
				else if (pCmd->GetID() == MC_UDP_PING)
				{
					delete pCmd;
				}
				else
				{
					LOG(LOG_FILE, "MMatchServer::ParseUDPPacket: NOT HANDLED COMMAND(%d)", pCmd->GetID());
					delete pCmd; //memory leak.
				}
			}
		}
		break;

Open(MMatchServer_OnCommand.cpp) <br>

Find <br>

	MC_MATCH_REQUEST_MONSTER_BIBLE_INFO


Replace <br>

		case MC_MATCH_REQUEST_MONSTER_BIBLE_INFO :
			{
				//MUID uidSender;

				//pCommand->GetParameter( &uidSender, 0, MPT_UID );

				//Exploit fix (UID spoofing)
				OnRequestMonsterBibleInfo(pCommand->GetSenderUID());
			}
			break;

Open(ZCombatInterface.cpp) <br>

Find

	m_pWeaponScreenEffect->Destroy();


Replace <br>

	//Fixed calling destructor of ZWeaponScreenEffect
	if (m_pWeaponScreenEffect)
		m_pWeaponScreenEffect->Destroy();

	SAFE_DELETE(m_pWeaponScreenEffect);


Open(ZGameClient.cpp) <br>

Find <br>

	ZGameClient::OnResponsePeerRelay


Replace <br>

	void ZGameClient::OnResponsePeerRelay(const MUID& uidPeer)
	{
		//Wolfie: Bug fix if NAT fails
		if (ZGetGame() == NULL) return;
		string strNotify = "Unknown Notify";
		NotifyMessage(MATCHNOTIFY_NETWORK_NAT_ESTABLISH, &strNotify);

		char* pszName = "UnknownPlayer";
		MMatchPeerInfo* pPeer = FindPeer(uidPeer);
		if (pPeer) pszName = pPeer->CharInfo.szName;

		char szMsg[128];
		sprintf(szMsg, "%s : from %s", strNotify.c_str(), pszName);


		ZCharacter* pChar = (ZCharacter*)ZGetCharacterManager()->Find( uidPeer);
		if ( pChar && pChar->IsAdminHide())
			return;

		ZChatOutput(szMsg, ZChat::CMT_SYSTEM);
	}

Open(ZGameClient_Ladder.cpp) <br>

Find <br>

	void ZGameClient::OnResponseProposal

Replace <br>

	void ZGameClient::OnResponseProposal(const int nResult, const MMatchProposalMode nProposalMode, const int nRequestID)
	{
		if (ZApplication::GetGameInterface()->GetState() != GUNZ_LOBBY)
		{
			//Small bugfix LadderCancel
			if (ZGetGameClient()->IsPlayerWars())
				ZPostLadderCancel(true);
			else
				ZPostLadderCancel(false);
			m_AgreementBuilder.CancelProposal();
			return;
		}


		if (nResult == MOK)
		{
			char szTitle[256];
			char szDesc[512];
			strcpy( szTitle, 
				ZMsg(MSG_LADDER_PROPOSAL_WAIT_TITLE) );

			strcpy(szDesc, 
				ZMsg(MSG_LADDER_PROPOSAL_WAIT_DESC) );

			ShowProposalWaitFrame(true, szTitle, szDesc);
		}
		else 
		{
			m_AgreementBuilder.CancelProposal();
			ZApplication::GetGameInterface()->ShowErrorMessage( nResult );
		}
	}

Open(ZGameInterface.cpp) <br>

Find <br>

	ZGameInterface::RequestQuickJoin()

Replace <br>

	void ZGameInterface::RequestQuickJoin()
	{
		//Fix quick join glitch (room tags)
		EnableLobbyInterface(false);

		MTD_QuickJoinParam	quick_join_param;

		quick_join_param.nMapEnum = 0xFFFFFFFF;

		quick_join_param.nModeEnum = 0;
		SetBitSet(quick_join_param.nModeEnum, MMATCH_GAMETYPE_DEATHMATCH_SOLO);
		SetBitSet(quick_join_param.nModeEnum, MMATCH_GAMETYPE_DEATHMATCH_TEAM);
		SetBitSet(quick_join_param.nModeEnum, MMATCH_GAMETYPE_ASSASSINATE);

		ZPostRequestQuickJoin(ZGetGameClient()->GetPlayerUID(), &quick_join_param);
	}

Find <br>

	m_bOnEndOfReplay = true;
	m_nLevelPercentCache = ZGetMyInfo()->GetLevelPercent();


Add above <br>

	//Wolfie: Fix for replay box showing in stage rooms, glitching the stage
	if (GetState() != GUNZ_LOBBY)
	{
		ShowReplayDialog(false);
		return;
	}

Open(ZItemSlotView.cpp) <br>

Find <br>

		if (nItemID != 0)
		{
			pItemDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);

Add under <br>

	if(!pItemDesc) return false;

Find <br>

	void ZItemSlotView::OnMouseIn( void )

Replace <br>

	void ZItemSlotView::OnMouseIn( void )
	{
		MButton::OnMouseIn();

		unsigned long int nItemID = ZGetMyInfo()->GetItemList()->GetEquipedItemID(m_nParts);
		MMatchItemDesc* pItemDesc = NULL;
		if (nItemID == 0) return;

		const char* szItemDescription = GetItemDescriptionWidgetName();
		MTextArea* pItemDescTextArea = (MTextArea*)ZGetGameInterface()->GetIDLResource()->FindWidget(szItemDescription);
		if (!pItemDescTextArea) return;

		pItemDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);

		//Item slot view bugfix crash
		if (!pItemDesc) return;

		ZMyItemNode* pItemNode = ZGetMyInfo()->GetItemList()->GetEquipedItem( m_nParts);
		MTextArea* pTextArea = (MTextArea*)ZGetGameInterface()->GetIDLResource()->FindWidget( szItemDescription);
		ZShopEquipItem_Match::FillItemDesc(pItemDesc, pTextArea, pItemNode);

		MRECT rcSlotView = GetRect();
		MRECT rcTextArea = pItemDescTextArea->GetRect();
		MPOINT posDesc(rcSlotView.x, rcSlotView.y);
		posDesc = MClientToScreen(GetParent(), posDesc);
		posDesc.x = rcSlotView.x + rcSlotView.w + CONVERT800(20);
		if (posDesc.y+rcTextArea.h > MGetWorkspaceHeight())
			posDesc.y = MGetWorkspaceHeight() - rcTextArea.h;
		pItemDescTextArea->SetPosition(posDesc);
		pItemDescTextArea->SetZOrder(MZ_TOP);
		ZGetGameInterface()->GetShopEquipInterface()->ShowItemDescription(true, pItemDescTextArea, this);
	}

Open(ZModule.cpp) <br>

Find <br>

	void ZModuleContainer::UpdateModules(float fElapsed)

Replace <br>

	void ZModuleContainer::UpdateModules(float fElapsed)
	{
		//Iterator fixes
		for (ZMODULEMAP::iterator i = m_ActiveModules.begin(); i != m_ActiveModules.end();) {
			ZModule *pModule = i->second;
			if (!pModule->Update(fElapsed)) {
				i = m_ActiveModules.erase(i);
			}
			else
				++i;
		}
	}

Open(ZModule_HPAP.h)

Find 

	__forceinline void ZModule_HPAP::SetHP(float fHP)


Replace

	__forceinline void ZModule_HPAP::SetHP(float fHP)
	{
		//Bugfix: Rounded DOWN damage to prevent 0 HP and 0 AP (they get 0/0 because it might be 0.1/0.2) 
		//fHP = /*floor(*/min(max(0,fHP),GetMaxHP());//);
		fHP = min(max(0, fHP), GetMaxHP());

		if (floor(fHP) <= 0.f)
			fHP = 0.f;

		m_fHP.Set_CheckCrc(fHP + GetMask());
	}

	__forceinline void ZModule_HPAP::SetAP(float fAP)
	{
		//Bugfix: Rounded UP damage to prevent 0 HP and 0 AP (they get 0/0 because it might be 0.1/0.2) 
		fAP = /*floor(*/min(max(0, fAP), GetMaxAP());//);

		if (floor(fAP) <= 0.f)
			fAP = 0.f;

		m_fAP.Set_CheckCrc(fAP + GetMask());
	}

Find

	void ZMyCharacter::ProcessDelayedWork()

Replace

	void ZMyCharacter::ProcessDelayedWork()
	{
		//Iterator increment fix
		for (ZDELAYEDWORKLIST::iterator i = m_DelayedWorkList.begin(); i != m_DelayedWorkList.end();)
		{
			ZDELAYEDWORKITEM *pItem = *i;

			// ½ÇÇàÇÒ ½Ã°£ÀÌ Áö³µÀ¸¸é ½ÇÇàÇÑ´Ù
			if (ZGetGame()->GetTime() > pItem->fTime)
			{
				OnDelayedWork(pItem);
				i = m_DelayedWorkList.erase(i);
				delete pItem;
			}
			else
				++i;
		}
	}

Find

	float ZMyCharacter::GetGravityConst()


Replace

	float ZMyCharacter::GetGravityConst()
	{
		ZCharaterStatusBitPacking & uStatus = m_dwStatusBitPackingValue.Ref();
		ZMyCharaterStatusBitPacking & zStatus = m_statusFlags.Ref();

		if(zStatus.m_bWallHang && zStatus.m_bHangSuccess) return 0;
		if(zStatus.m_bShot) return 0;

		if(uStatus.m_bBlastFall) return .7f;

		if(uStatus.m_bWallJump)
		{
			if(m_nWallJumpDir.Ref()==1 || GetVelocity().z<0)
				return 0;
			else
				return .1f;
		}

		if(zStatus.m_bSlash)
		{
			//Bug fix (No item crash)
			if (GetItems()->GetItem(MMCIP_MELEE) == NULL)
			{
				return 1.f;
			}

			MMatchItemDesc *pDesc = GetItems()->GetItem(MMCIP_MELEE)->GetDesc();
			//Bug fix (pDesc NULL check)
			if (pDesc != NULL && pDesc->m_nWeaponType.Ref()==MWT_DOUBLE_KATANA) {
				AniFrameInfo* pAniLow = m_pVMesh->GetFrameInfo(ani_mode_lower);
				if( pAniLow->m_nFrame < 160*11) return 0;
			}
		}

		if(zStatus.m_bSkill) {
			//Bug fix (No item crash)
			if (GetItems()->GetItem(MMCIP_MELEE) == NULL)
			{
				return 1.f;
			}

			MMatchItemDesc *pDesc = GetItems()->GetItem(MMCIP_MELEE)->GetDesc();
			//Bug fix (pDesc NULL check)
			if (pDesc != NULL && pDesc->m_nWeaponType.Ref() == MWT_DOUBLE_KATANA) {
				AniFrameInfo* pAniLow = m_pVMesh->GetFrameInfo(ani_mode_lower);
				if (pAniLow->m_nFrame < 160 * 20) return 0;
			}
		}

		return 1.f;
	}

Find <br>

	if( m_pVMesh->m_SelectWeaponMotionType==eq_ws_dagger )
	{ // dagger
		if(m_AniState_Upper.Ref()==ZC_STATE_UPPER_SHOT)
			return;
      
Replace <br>

	if( m_pVMesh->m_SelectWeaponMotionType==eq_wd_dagger ||
		m_pVMesh->m_SelectWeaponMotionType==eq_ws_dagger ) 
	{ // dagger
		if(m_AniState_Upper.Ref()==ZC_STATE_UPPER_SHOT)
			return;

Open(ZObject.cpp) <br>

Find <br>

	void ZObject::ShiftFugitiveValues()

Replace <br>

	void ZObject::ShiftFugitiveValues()
	{
		if (m_pVMesh) 
		{
			//Disable m_pVMesh antihack
			/*
			// AniFramInfo
			{
				AniFrameInfo* old = m_pVMesh->m_FrameInfo;
				AniFrameInfo* New = new AniFrameInfo[ani_mode_end];

				for (int i=0; i<ani_mode_end; ++i)
					New[i] = old[i];
				delete [] old;

				m_pVMesh->m_FrameInfo = New;
			}

			{
				if (m_pVMesh->m_SelectWeaponMotionType !=
					*m_pVMesh->m_pSelectWeaponMotionType_AntiHack)
					g_fpOnCrcFail();

				RWeaponMotionType* old = m_pVMesh->m_pSelectWeaponMotionType_AntiHack;
				RWeaponMotionType* New = new RWeaponMotionType;

				*New = *old;
				delete old;

				m_pVMesh->m_pSelectWeaponMotionType_AntiHack = New;
			}
			*/
		}

		if (m_pModule_Movable)
			m_pModule_Movable->ShiftFugitiveValues();

		m_Items.ShiftFugitiveValues();

		m_Position.ShiftHeapPos_CheckCrc();

		m_Collision.ShiftFugitiveValues();
	}

Open(ZGameInterface.cpp) <br>

Find <br>

	char* GetItemSlotName( const char* szName, int nItem)

Replace <br>

	char* GetItemSlotName( const char* szName, int nItem)
	{
		static char szTemp[256];
		strcpy( szTemp, szName);

		switch (nItem)
		{
			case MMCIP_HEAD :		strcat( szTemp, "_EquipmentSlot_Head");			break;
			case MMCIP_CHEST :		strcat( szTemp, "_EquipmentSlot_Chest");		break;
			case MMCIP_HANDS :		strcat( szTemp, "_EquipmentSlot_Hands");		break;
			case MMCIP_LEGS :		strcat( szTemp, "_EquipmentSlot_Legs");			break;
			case MMCIP_FEET :		strcat( szTemp, "_EquipmentSlot_Feet");			break;
			case MMCIP_FINGERL :	strcat( szTemp, "_EquipmentSlot_FingerL");		break;
			case MMCIP_FINGERR :	strcat( szTemp, "_EquipmentSlot_FingerR");		break;
			case MMCIP_AVATAR :		strcat( szTemp, "_EquipmentSlot_Avatar");		break;
			case MMCIP_MELEE :		strcat( szTemp, "_EquipmentSlot_Melee");		break;
			case MMCIP_PRIMARY :	strcat( szTemp, "_EquipmentSlot_Primary");		break;
			case MMCIP_SECONDARY :	strcat( szTemp, "_EquipmentSlot_Secondary");	break;
			case MMCIP_CUSTOM1 :	strcat( szTemp, "_EquipmentSlot_Custom1");		break;
			case MMCIP_CUSTOM2 :	strcat( szTemp, "_EquipmentSlot_Custom2");		break;
			case MMCIP_COMMUNITY1:	strcat( szTemp, "_EquipmentSlot_Community1");	break;
			case MMCIP_COMMUNITY2:	strcat( szTemp, "_EquipmentSlot_Community2");	break;
			case MMCIP_LONGBUFF1 :	strcat( szTemp, "_EquipmentSlot_LongBuff1");	break;
			case MMCIP_LONGBUFF2 :	strcat( szTemp, "_EquipmentSlot_LongBuff2");	break;

			default: _ASSERT(0); break;
		}

		return szTemp;
	}


Find <br>

	void ZGameInterface::OnNetmarbleLoginCreate(void)

Replace <br>

	#include "ZNetmarble.h"
	void ZGameInterface::OnNetmarbleLoginCreate(void)
	{
		if ( m_pBackground)
		{
			m_pBackground->LoadMesh();
			m_pBackground->SetScene(LOGIN_SCENE_FIXEDSKY);
		}

		ZApplication::GetSoundEngine()->StopMusic();
		ZApplication::GetSoundEngine()->OpenMusic( BGMID_INTRO, ZApplication::GetFileSystem());

		if (m_spGameClient->IsConnected())
		{
			ZPostDisconnect();
		}

		HideAllWidgets();
		ShowWidget("NetmarbleLogin", true);

		ZBaseAuthInfo* pAuthInfo = ZGetLocale()->GetAuthInfo();
		if (pAuthInfo)
		{
	#ifdef _DEBUG
			mlog("Connect to Netmarble GunzServer(IP:%s , Port:%d) \n", pAuthInfo->GetServerIP(), pAuthInfo->GetServerPort());
	#endif
			ZPostConnect(pAuthInfo->GetServerIP(), pAuthInfo->GetServerPort());
		}
		else _ASSERT(0);
	}

Find <br>

	void ZGameInterface::OnGameOnLoginCreate(void)

Replace <br>

	void ZGameInterface::OnGameOnLoginCreate(void)
	{
		if ( m_pBackground)
		{
			m_pBackground->LoadMesh();
			m_pBackground->SetScene(LOGIN_SCENE_FIXEDSKY);
		}

		ZApplication::GetSoundEngine()->StopMusic();
		ZApplication::GetSoundEngine()->OpenMusic( BGMID_INTRO, ZApplication::GetFileSystem());

		if (m_spGameClient->IsConnected())
		{
			ZPostDisconnect();
		}

		HideAllWidgets();
		ShowWidget("NetmarbleLogin", true);

		ZBaseAuthInfo* pAuthInfo = ZGetLocale()->GetAuthInfo();
		if (pAuthInfo)
			ZPostConnect( pAuthInfo->GetServerIP(), pAuthInfo->GetServerPort());
		else
			_ASSERT(0);
	}

Find <br>

		switch (nType)
		{
		case ZCWT_MELEE:	nParts = (int)(MMCIP_MELEE);	break;
		case ZCWT_PRIMARY:	nParts = (int)(MMCIP_PRIMARY);	break;
		case ZCWT_SECONDARY:nParts = (int)(MMCIP_SECONDARY);break;
		case ZCWT_CUSTOM1:	nParts = (int)(MMCIP_CUSTOM1);	break;
		case ZCWT_CUSTOM2:	nParts = (int)(MMCIP_CUSTOM2);	break;
		default: break;
		}

Replace <br>

		switch (nType)
		{
		case ZCWT_MELEE:	nParts = (int)(MMCIP_MELEE);	break;
		case ZCWT_PRIMARY:	nParts = (int)(MMCIP_PRIMARY);	break;
		case ZCWT_SECONDARY:nParts = (int)(MMCIP_SECONDARY);break;
		case ZCWT_CUSTOM1:	nParts = (int)(MMCIP_CUSTOM1);	break;
		case ZCWT_CUSTOM2:	nParts = (int)(MMCIP_CUSTOM2);	break;
		default: _ASSERT(0); break;
		}


Open(main.cpp) <br>
Find <br>

	if (0 != RInitD3D(&g_ModeParams))

Replace <br>

	if (0 != RInitD3D(&g_ModeParams))
	{
		if (MessageBox(NULL, "Failed to initialize DirectX,\nDirectX 9.0 or higher is required,\nWould you like the download page opened?\n\nFailed to initialize DirectX,\nDirectX 9.0 or higher is required,\nDo you want to open the page to download it to the be able to install ?\n\nI cannot initialize DirectX,\nDirectX 9.0 or higher is required,\n¿Would you like to open the download page?\n\nMislukt DirectX,\nDirectX 9.0 of hoger te initialiseren nodig is,\nWilt u download page geopend?", "DirectX Check|DirectX Check|DirectX Check|DirectX Control", MB_YESNO) == IDYES)
		{
			ShellExecute(NULL, "open", "http://www.microsoft.com/en-gb/download/details.aspx?id=35", NULL, NULL, SW_SHOWNORMAL);
		}
		DeleteFile("config.xml");
		mlog("error init RInitD3D\n");
		return 0;
	}

Open(ZItem.cpp) <br>
Find <br>

		case MWT_REVOLVER:
		case MWT_REVOLVERx2:
			{
				if(bHead)	fRatio = 0.75f; //0.9
				else		fRatio = 0.55f; //0.7
			}
			break;

Replace <br>

		case MWT_REVOLVER:
		case MWT_REVOLVERx2:
			{
				if(bHead)	fRatio = 0.8f;
				else		fRatio = 0.6f;
			}
			break;

Open(Gunz/stdafx.h) <br>
Find <br>

	#define _MULTILANGUAGE
	#endif

Replace <br>

	#if defined(_DEBUG) || defined(_RELEASE) || defined(LOCALE_KOREA) || defined(LOCALE_NHNUSA) || defined(LOCALE_JAPAN) || defined(LOCALE_BRAZIL)
	#define _MULTILANGUAGE
	#endif

Open(ZSoundEngine.cpp) <br>
Find <br>

	void ZSoundEngine::PlaySoundBladeConcrete(MMatchItemDesc *pDesc, rvector pos)

Replace <br>

	void ZSoundEngine::PlaySoundBladeConcrete(MMatchItemDesc *pDesc, rvector pos)
	{
		if (NULL != pDesc && strcmp("rubber", pDesc->m_szWeaponByFiber) == 0)
		{
			PlaySound("rubber_concrete", pos);
			return;
		}

		PlaySound("blade_concrete", pos);
	}
	void ZSoundEngine::PlaySoundBladeDamage(MMatchItemDesc *pDesc, rvector& pos)
	{
		if (NULL != pDesc && strcmp("rubber", pDesc->m_szWeaponByFiber) == 0)
		{
			PlaySound("rubber_damage", pos);
			return;
		}

		PlaySound("blade_damage", pos);
	}
	void ZSoundEngine::PlaySoundHangOnWall(MMatchItemDesc *pDesc, rvector& pos)
	{
		if (NULL != pDesc && strcmp("rubber", pDesc->m_szWeaponByFiber) == 0)
		{
			PlaySound("rubber_hangonwall", pos);
			return;
		}

		PlaySound("hangonwall", pos);
	}
	void ZSoundEngine::PlaySoundChargeComplete(MMatchItemDesc *pDesc, const rvector& pos)
	{
		if (NULL != pDesc && strcmp("rubber", pDesc->m_szWeaponByFiber) == 0)
		{
			PlaySound("fx2/FX_rubber_ChargeComplete", pos);
			return;
		}

		PlaySound("fx2/FX_ChargeComplete", pos);
	}
	void ZSoundEngine::PlaySoundSmash(MMatchItemDesc *pDesc, rvector& pos, bool bObserverTarget)
	{
		if (NULL != pDesc && strcmp("rubber", pDesc->m_szWeaponByFiber) == 0)
		{
			PlaySound(bObserverTarget ? "we_rubber_smash_2d" : "we_rubber_smash", pos);
			return;
		}

		PlaySound(bObserverTarget ? "we_smash_2d" : "we_smash", pos);
	}

	void ZSoundEngine::PlaySoundSheath(MMatchItemDesc *pDesc, const rvector& pos, bool bObserverTarget)
	{
		if (NULL != pDesc && strcmp("rubber", pDesc->m_szWeaponByFiber) == 0)
		{
			PlaySound("fx_rubber_sheath", pos, bObserverTarget);
			return;
		}

		PlaySound("fx_blade_sheath", pos, bObserverTarget);
	}

Open(ZShopEquipListBox.cpp) <br>
Find <br>

	if (bSelected)
		pDC->SetColor(220, 220, 220);
	else if (bMouseOver)
		pDC->SetColor(60, 60, 60);
	else
		pDC->SetColor(19, 19, 19);

Replace <br>

	if (bSelected)
		pDC->SetColor(220, 220, 220);
	else if (bMouseOver)
		pDC->SetColor(60, 60, 60);
	else
		pDC->SetColor(20, 20, 20);

Find <br>

	if (ZSEIT_MATCH == m_pItemData->GetType())
	{
		int hint = 2000000;
		int nResLv = m_pItemData->GetLevelRes();
		MMatchItemDesc* pItemDesc = ((ZShopEquipItem_Match*)m_pItemData)->GetDesc();
		if (!pItemDesc) { return 0; }

Replace <br>

	if (ZSEIT_MATCH == m_pItemData->GetType())
	{
		int hint = 2000000;
		int nResLv = m_pItemData->GetLevelRes();
		MMatchItemDesc* pItemDesc = ((ZShopEquipItem_Match*)m_pItemData)->GetDesc();
		if (!pItemDesc) { _ASSERT(0); return 0; }



Open(ZGameInterface.cpp) <br>

Find <br>


    MLabel* pLabel = (MLabel*)pResource->FindWidget("Shop_Message");
    sprintf( buf, "%s > %s > %s", ZGetGameClient()->GetServerName(), ZMsg( MSG_WORD_LOBBY), ZMsg( MSG_WORD_SHOP));

Replace <br>

		char buf[256];
		MLabel* pLabel = (MLabel*)pResource->FindWidget("Shop_Message");
		if (ZApplication::GetGameInterface()->GetState() == GUNZ_LOBBY)
			sprintf(buf, "%s > %s > %s", ZGetGameClient()->GetServerName(), ZMsg(MSG_WORD_LOBBY), ZMsg(MSG_WORD_SHOP));
		else
			sprintf(buf, "%s > %s > %s", ZGetGameClient()->GetServerName(), ZMsg(MSG_WORD_STAGE), ZMsg(MSG_WORD_SHOP));

Find <br>

    if (ZApplication::GetGameInterface()->GetState() == GUNZ_LOBBY)

Replace <br>

		if (ZApplication::GetGameInterface()->GetState() == GUNZ_LOBBY)
			ShowWidget("Lobby", true);
		else
			ShowWidget("Stage", true);


Open(ZPlayerListBox.cpp) <br>
Find <br>

	if((gid==MMUG_DEVELOPER) || (gid==MMUG_ADMIN)) {

Replace

	//if((gid==MMUG_DEVELOPER) || (gid==MMUG_ADMIN)) {
	//	bShow = false;
	//}

Find <br>

					switch(GetMode()) {
					case PLAYERLISTMODE_CHANNEL:
						pMenu->SetupMenu(ZPLAYERMENU_SET_LOBBY);
						break;
					case PLAYERLISTMODE_STAGE:
						pMenu->SetupMenu(ZPLAYERMENU_SET_STAGE);
						break;
					case PLAYERLISTMODE_CHANNEL_FRIEND:
						pMenu->SetupMenu(ZPLAYERMENU_SET_FRIEND);
						break;
					case PLAYERLISTMODE_STAGE_FRIEND:
						pMenu->SetupMenu(ZPLAYERMENU_SET_FRIEND);
						break;
					case PLAYERLISTMODE_CHANNEL_CLAN:
						if(pItem->GetUID() == ZGetMyUID())
							pMenu->SetupMenu(ZPLAYERMENU_SET_CLAN_ME);
						else
							pMenu->SetupMenu(ZPLAYERMENU_SET_CLAN);
						break;
					default:
					break;
						//_ASSERT("Unknown PlayerMenu Setup");
					};

Replace <br>

					switch(GetMode()) {
					case PLAYERLISTMODE_CHANNEL:
						pMenu->SetupMenu(ZPLAYERMENU_SET_LOBBY);
						break;
					case PLAYERLISTMODE_STAGE:
						pMenu->SetupMenu(ZPLAYERMENU_SET_STAGE);
						break;
					case PLAYERLISTMODE_CHANNEL_FRIEND:
						pMenu->SetupMenu(ZPLAYERMENU_SET_FRIEND);
						break;
					case PLAYERLISTMODE_STAGE_FRIEND:
						pMenu->SetupMenu(ZPLAYERMENU_SET_FRIEND);
						break;
					case PLAYERLISTMODE_CHANNEL_CLAN:
						if(pItem->GetUID() == ZGetMyUID())
							pMenu->SetupMenu(ZPLAYERMENU_SET_CLAN_ME);
						else
							pMenu->SetupMenu(ZPLAYERMENU_SET_CLAN);
						break;
					default:
						_ASSERT("Unknown PlayerMenu Setup");
					};


Open(ZDuelTournamentRankingListBox.cpp) <br>

Find <br>

	if (nIndex >= NUM_DISPLAY_DUELTOURNAMENT_RANKING) { return; }//_ASSERT(0); return; }


Replace <br>

	if (nIndex >= NUM_DISPLAY_DUELTOURNAMENT_RANKING) { _ASSERT(0); return; }

Find <br>

	if (!m_pBmpRankingItemBg || !m_pBmpArrowUp || !m_pBmpArrowDown || !m_pBmpArrowBar) {  return; }

Replace <br>

	if (!m_pBmpRankingItemBg || !m_pBmpArrowUp || !m_pBmpArrowDown || !m_pBmpArrowBar) { _ASSERT(0); return; }


Find <br>

	rc.Set((int)(0.01f*nWidth), y, (int)(fabs(0.01f - 0.11f)*nWidth), nItemHeight);

Replace <br>

	rc.Set((int)(0.01f*nWidth), y, (int)(fabs(0.01f - 0.23f)*nWidth), nItemHeight);

Find <br>

	rc.Set((int)(0.8f*nWidth), y, (int)(fabs(0.8f - 0.91f)*nWidth), nItemHeight);

Replace <br>

	rc.Set((int)(0.8f*nWidth), y, (int)(fabs(0.8f - 0.85f)*nWidth), nItemHeight);

Open(ZCharacterView.cpp) <br>
Find <br>

			if(GetUserInfoUID(m_Info.UID,_color,sp_name,gid)) {//Æ¯º°ÇÑ À¯ÀúÀÎ °æ¿ì
				SetText( sp_name );
				m_Info.nLevel = 0;//pObj->GetLevel();
			}
			else {
				SetText( pObj->GetName() );
				m_Info.nLevel = pObj->GetLevel();
			}

Replace <br>

			GetUserInfoUID(m_Info.UID, _color, sp_name, gid);
			SetText(pObj->GetName());
			m_Info.nLevel = pObj->GetLevel();

Open(ZMinimap.cpp) <br>
Find <br>

	pDC->SetFont(pFont);

Place above <br>
		
	if (pFont == NULL) _ASSERT(0);


Open(ZShopEquipItem.cpp) <br>
Find <br>

	void IShopEquipItemHandle_Purchase::OpenSimpleConfirmDlg(ISimpleConfirmDlgDoneHandler* pDoneHandler)
	{
		ZSimpleConfirmDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetSimpleConfirmDlg();
		if (!pDlg) { return; }
		pDlg->Open(ZMsg(MSG_SHOPEQUIP_BUY_CONFIRM), pDoneHandler);
	}

Replace <br>

	void IShopEquipItemHandle_Purchase::OpenSimpleConfirmDlg(ISimpleConfirmDlgDoneHandler* pDoneHandler)
	{
		ZSimpleConfirmDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetSimpleConfirmDlg();
		if (!pDlg) { _ASSERT(0); return; }
		pDlg->Open(ZMsg(MSG_SHOPEQUIP_BUY_CONFIRM), pDoneHandler);
	}

	void IShopEquipItemHandle_Purchase::OpenCountableConfirmDlg(
		const char* szItemName, MBitmap* pIconBmp, int nPrice, int nMax, IItemCountDlgDoneHandler* pDoneHandler)
	{
		ZItemCountDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetItemCountDlg();
		if (!pDlg) { _ASSERT(0); return; }
		pDlg->Open(ZICD_BUY, szItemName, pIconBmp, nPrice, 0, nMax, pDoneHandler);
	}


	// ============= ZShopEquipItemHandle_PurchaseGamble ±¸¸Å ÇÚµé·¯ : °·ºí================================================

	ZShopEquipItemHandle_PurchaseGamble::ZShopEquipItemHandle_PurchaseGamble(ZShopEquipItem_Gamble* pItem)
	{
		m_pItem = pItem;
		_ASSERT(m_pItem);
	}

	void ZShopEquipItemHandle_PurchaseGamble::Buy()
	{
		if (!m_pItem->GetDesc()) { _ASSERT(0); return; }
		if (!m_pItem->GetDesc()->IsCash())
		{
			OpenSimpleConfirmDlg(this);
		}
		else
		{
	#ifndef _PUBLISH
			OpenSimpleConfirmDlg(this);//todok ÀÓ½Ã·Î Ä³½¬ÅÛÀ» ¹Ù¿îÆ¼·Î ±¸¸Å °¡´É
	#endif
		}
	}

	void ZShopEquipItemHandle_PurchaseGamble::OnDone(bool bOk)
	{
		if (!bOk) return;

		ZPostRequestBuyItem(ZGetMyUID(), m_pItem->GetDesc()->GetGambleItemID(), 1);
		ZPostRequestCharacterItemList(ZGetGameClient()->GetPlayerUID());
	}

	bool ZShopEquipItemHandle_PurchaseGamble::GetPrice(bool& out_bCash, bool& out_bEvent, int& out_nRentalHour, int& out_nPrice)
	{
		if (!m_pItem || !m_pItem->GetDesc()) { _ASSERT(0); return false; }
		out_bCash = m_pItem->GetDesc()->IsCash();
		//out_bEvent = m_pItem->GetDesc()->IsEventItem();
		out_nRentalHour = 0;
		out_nPrice = m_pItem->GetDesc()->GetPrice();
		return true;
	}

	// ============= ZShopEquipItemHandle_PurchaseMatch ±¸¸ÅÇÚµé·¯ : Àåºñ ================================================

	ZShopEquipItemHandle_PurchaseMatch::ZShopEquipItemHandle_PurchaseMatch(ZShopEquipItem_Match* pItem)
	{
		m_pItem = pItem;
		_ASSERT(m_pItem);
	}

Find <br>

	if (!m_pItem->GetDesc()) { return; }

Replace <br>

	if (!m_pItem->GetDesc()) { _ASSERT(0); return; }

Find <br>

	if (!m_pItem || !m_pItem->GetDesc()) { return false; }

Replace <br>

	if (!m_pItem || !m_pItem->GetDesc()) { _ASSERT(0); return false; }


Find <br>

	if (!m_pItem->GetDesc()) { return; }

Replace <br>

	if (!m_pItem->GetDesc()) { _ASSERT(0); return; }

Find <br>

	if (!m_pItem || !m_pItem->GetDesc()) { return false; }

Replace <br>

	if (!m_pItem || !m_pItem->GetDesc()) { _ASSERT(0); return false; }

Find 

	void IShopEquipItemHandle_Sell::OpenCountableConfirmDlg(const char* szItemName, MBitmap* pIconBmp, int nPrice, int nMax, IItemCountDlgDoneHandler* pDoneHandler)
	{
		ZItemCountDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetItemCountDlg();
		if (!pDlg) { return; }
		pDlg->Open(ZICD_SELL, szItemName, pIconBmp, nPrice, 0, nMax, pDoneHandler);
	}

	void IShopEquipItemHandle_Sell::OpenSimpleConfirmDlg( ISimpleConfirmDlgDoneHandler* pDoneHandler )
	{
		ZSimpleConfirmDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetSimpleConfirmDlg();
		if (!pDlg) { return; }
		pDlg->Open(ZMsg(MSG_SHOPEQUIP_SELL_CONFIRM), pDoneHandler);
	}

	void IShopEquipItemHandle_Sell::OpenSellCashItemConfirmDlg( const char* szItemName, MBitmap* pIcon, int price, int count, ISellCashItemConfirmDlgDoneHandler* pHandler )
	{
		ZSellCashItemConfirmDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetSellCashItemConfirmDlg();
		if (!pDlg) {  return; }
		pDlg->Open(szItemName, pIcon, price, count, pHandler);
	}

Replace <br>

	void IShopEquipItemHandle_Sell::OpenCountableConfirmDlg(const char* szItemName, MBitmap* pIconBmp, int nPrice, int nMax, IItemCountDlgDoneHandler* pDoneHandler)
	{
		ZItemCountDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetItemCountDlg();
		if (!pDlg) { _ASSERT(0); return; }
		pDlg->Open(ZICD_SELL, szItemName, pIconBmp, nPrice, 0, nMax, pDoneHandler);
	}

	void IShopEquipItemHandle_Sell::OpenSimpleConfirmDlg(ISimpleConfirmDlgDoneHandler* pDoneHandler)
	{
		ZSimpleConfirmDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetSimpleConfirmDlg();
		if (!pDlg) { _ASSERT(0); return; }
		pDlg->Open(ZMsg(MSG_SHOPEQUIP_SELL_CONFIRM), pDoneHandler);
	}

	void IShopEquipItemHandle_Sell::OpenSellCashItemConfirmDlg(const char* szItemName, MBitmap* pIcon, int price, int count, ISellCashItemConfirmDlgDoneHandler* pHandler)
	{
		ZSellCashItemConfirmDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetSellCashItemConfirmDlg();
		if (!pDlg) { _ASSERT(0); return; }
		pDlg->Open(szItemName, pIcon, price, count, pHandler);
	}

Find <br>

	const ZGambleItemDefine* pDesc = m_pItem->GetDesc();
	if (!pDesc) { return false; }

Replace <br>

	const ZGambleItemDefine* pDesc = m_pItem->GetDesc();
	if (!pDesc) { _ASSERT(pDesc); return false; }


Find <br>

	void ZShopEquipItemHandle_SellGamble::Sell()
	{
		if (!m_pItem->GetDesc()) { return; }

	#ifndef _SELL_CASHITEM
		if (m_pItem->GetDesc()->IsCash()) { return; }
	#endif

		int nPrice = 0;
		if (!GetPrice(nPrice)) return;

		const ZMyGambleItem *pMyGItem = ZGetMyInfo()->GetItemList()->GetGambleItem( GetItemUID());
		if (!pMyGItem)  { return; }
		int nMax = pMyGItem->GetItemCount();

		OpenCountableConfirmDlg(
			m_pItem->GetDesc()->GetName().c_str(),
			m_pItem->GetIcon(),
			nPrice,
			nMax, this);
	}


Replace <br>

	void ZShopEquipItemHandle_SellGamble::Sell()
	{
		if (!m_pItem->GetDesc()) { _ASSERT(0); return; }

	#ifndef _SELL_CASHITEM
		if (m_pItem->GetDesc()->IsCash()) { _ASSERT(0); return; }
	#endif

		int nPrice = 0;
		if (!GetPrice(nPrice)) return;

		const ZMyGambleItem *pMyGItem = ZGetMyInfo()->GetItemList()->GetGambleItem(GetItemUID());
		if (!pMyGItem)  { _ASSERT(0); return; }
		int nMax = pMyGItem->GetItemCount();

		OpenCountableConfirmDlg(
			m_pItem->GetDesc()->GetName().c_str(),
			m_pItem->GetIcon(),
			nPrice,
			nMax, this);
	}


Find <br>

	MMatchItemDesc* pDesc = m_pItem->GetDesc();
	if (!pDesc) {  return false; }

Replace <br>

	MMatchItemDesc* pDesc = m_pItem->GetDesc();
	if (!pDesc) { _ASSERT(pDesc); return false; }


Find <br>

	void ZShopEquipItemHandle_SellMatch::Sell()
	{
		if (!m_pItem->GetDesc()) {  return; }

	#ifndef _SELL_CASHITEM
		if (m_pItem->GetDesc()->IsCashItem()) {  return; }
	#endif

Replace <br>

	void ZShopEquipItemHandle_SellMatch::Sell()
	{
		if (!m_pItem->GetDesc()) { _ASSERT(0); return; }

	#ifndef _SELL_CASHITEM
		if (m_pItem->GetDesc()->IsCashItem()) { _ASSERT(0); return; }
	#endif


Find <br>

		ZMyItemNode* pMyItemNode = ZGetMyInfo()->GetItemList()->GetItem( GetItemUID());
		if (!pMyItemNode) {  return; }
		
Replace <br>

		ZMyItemNode* pMyItemNode = ZGetMyInfo()->GetItemList()->GetItem(GetItemUID());
		if (!pMyItemNode) { _ASSERT(0); return; }

Find <br>

	MQuestItemDesc* pDesc = m_pItem->GetDesc();
	if (!pDesc) { return false; }

Replace <br>

	MQuestItemDesc* pDesc = m_pItem->GetDesc();
	if (!pDesc) { _ASSERT(pDesc); return false; }


Find <br>

	void ZShopEquipItemHandle_SellQuest::Sell()
	{
		if (!m_pItem->GetDesc()) { return; }
		int nPrice = 0;
		if (!GetPrice(nPrice)) return;

		ZMyQuestItemNode* pMyQItem = ZGetMyInfo()->GetItemList()->GetQuestItemMap().Find( m_pItem->GetDesc()->m_nItemID);
		if (!pMyQItem) { return; }

		int nMax = pMyQItem->GetCount();

		OpenCountableConfirmDlg(
			m_pItem->GetDesc()->m_szQuestItemName,
			m_pItem->GetIcon(),
			nPrice,
			nMax, this);
	}

Replace <br>

	void ZShopEquipItemHandle_SellQuest::Sell()
	{
		if (!m_pItem->GetDesc()) { _ASSERT(0); return; }
		int nPrice = 0;
		if (!GetPrice(nPrice)) return;

		ZMyQuestItemNode* pMyQItem = ZGetMyInfo()->GetItemList()->GetQuestItemMap().Find(m_pItem->GetDesc()->m_nItemID);
		if (!pMyQItem) { _ASSERT(0); return; }

		int nMax = pMyQItem->GetCount();

		OpenCountableConfirmDlg(
			m_pItem->GetDesc()->m_szQuestItemName,
			m_pItem->GetIcon(),
			nPrice,
			nMax, this);
	}


Find <br>

	void ZShopEquipItemHandle_SellQuest::OnDone(int nCount)
	{
		if (!m_pItem->GetDesc()) { return; }

		ZPostRequestSellQuestItem( ZGetGameClient()->GetPlayerUID(), m_pItem->GetDesc()->m_nItemID, nCount);
	}


Replace <br>

	void ZShopEquipItemHandle_SellQuest::OnDone(int nCount)
	{
		if (!m_pItem->GetDesc()) { _ASSERT(0); return; }

		ZPostRequestSellQuestItem(ZGetGameClient()->GetPlayerUID(), m_pItem->GetDesc()->m_nItemID, nCount);
	}

Find <br>

	if (!m_pItem->GetDesc()) { return; }
	if (!m_pItem->CanSendAccount()) return;

Replace <br>

	if (!m_pItem->GetDesc()) { _ASSERT(0); return; }
	if (!m_pItem->CanSendAccount()) return;

Find <br>

	if (!m_pItem->GetDesc()) { return; }
	if (!m_pItem->CanSendAccount()) return;

Replace <br>

	if (!m_pItem->GetDesc()) { _ASSERT(0); return; }
	if (!m_pItem->CanSendAccount()) return;

Find <br>

	void ZShopEquipItemHandle_SendAccountMatch::Send()
	{
		if (!m_pItem->GetDesc()) { return; }
		if (!m_pItem->CanSendAccount()) { return; }

		if (m_pItem->GetDesc()->IsSpendableItem())
		{
			ZMyItemNode* pMyItemNode = ZGetMyInfo()->GetItemList()->GetItem( GetItemUID());
			if (!pMyItemNode) { return; }

			int nMax = pMyItemNode->GetItemCount();

			OpenCountableConfirmDlg(
				m_pItem->GetDesc()->m_pMItemName->Ref().m_szItemName,
				m_pItem->GetIcon(),
				0,
				nMax, this);	
		}
		else
		{
			ZPostRequestSendAccountItem(ZGetMyUID(), GetItemUID());
		}
	}

Replace <br>

	void ZShopEquipItemHandle_SendAccountMatch::Send()
	{
		if (!m_pItem->GetDesc()) { _ASSERT(0); return; }
		if (!m_pItem->CanSendAccount()) { _ASSERT(0); return; }

		if (m_pItem->GetDesc()->IsSpendableItem())
		{
			ZMyItemNode* pMyItemNode = ZGetMyInfo()->GetItemList()->GetItem(GetItemUID());
			if (!pMyItemNode) { _ASSERT(0); return; }

			int nMax = pMyItemNode->GetItemCount();

			OpenCountableConfirmDlg(
				m_pItem->GetDesc()->m_pMItemName->Ref().m_szItemName,
				m_pItem->GetIcon(),
				0,
				nMax, this);
		}
		else
		{
			ZPostRequestSendAccountItem(ZGetMyUID(), GetItemUID());
		}
	}


Find <br>

	void ZShopEquipItemHandle_SendAccountMatch::OnDone(int nCount)
	{
		if (!m_pItem->GetDesc()) { return; }
		if (!m_pItem->CanSendAccount()) return;

		if (nCount <= 0) return;

		ZPostRequestSendAccountItem(ZGetMyUID(), GetItemUID(), nCount);
	}

Replace <br>

	void ZShopEquipItemHandle_SendAccountMatch::OnDone(int nCount)
	{
		if (!m_pItem->GetDesc()) { _ASSERT(0); return; }
		if (!m_pItem->CanSendAccount()) return;

		if (nCount <= 0) return;

		ZPostRequestSendAccountItem(ZGetMyUID(), GetItemUID(), nCount);
	}

Find <br>

	void IShopEquipItemHandle_BringAccount::OpenSimpleConfirmDlg( ISimpleConfirmDlgDoneHandler* pDoneHandler )
	{
		ZSimpleConfirmDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetSimpleConfirmDlg();
		if (!pDlg) { return; }
		pDlg->Open(ZMsg(MSG_SHOPEQUIP_BRING_CONFIRM), pDoneHandler);
	}

	void IShopEquipItemHandle_BringAccount::OpenCountableConfirmDlg( const char* szItemName, MBitmap* pIconBmp, int nPrice, int nMax, IItemCountDlgDoneHandler* pDoneHandler )
	{
		ZItemCountDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetItemCountDlg();
		if (!pDlg) { return; }
		pDlg->Open(ZICD_BRINGACCOUNT, szItemName, pIconBmp, nPrice, 0, nMax, pDoneHandler);
	}


Replace <br>

	void IShopEquipItemHandle_BringAccount::OpenSimpleConfirmDlg(ISimpleConfirmDlgDoneHandler* pDoneHandler)
	{
		ZSimpleConfirmDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetSimpleConfirmDlg();
		if (!pDlg) { _ASSERT(0); return; }
		pDlg->Open(ZMsg(MSG_SHOPEQUIP_BRING_CONFIRM), pDoneHandler);
	}

	void IShopEquipItemHandle_BringAccount::OpenCountableConfirmDlg(const char* szItemName, MBitmap* pIconBmp, int nPrice, int nMax, IItemCountDlgDoneHandler* pDoneHandler)
	{
		ZItemCountDlg* pDlg = ZGetGameInterface()->GetShopEquipInterface()->GetItemCountDlg();
		if (!pDlg) { _ASSERT(0); return; }
		pDlg->Open(ZICD_BRINGACCOUNT, szItemName, pIconBmp, nPrice, 0, nMax, pDoneHandler);
	}

Find <br>

	if (!m_pItem->GetDesc()) { return; }
	if (GetAIID() == 0) return;

Replace <br>

	if (!m_pItem->GetDesc()) { _ASSERT(0); return; }
	if (GetAIID() == 0) return;

Find <br>

	if (!m_pItem->GetDesc()) { return; }
	if (GetAIID() == 0) return;

Replace <br>

	if (!m_pItem->GetDesc()) { _ASSERT(0); return; }
	if (GetAIID() == 0) return;


Open(ZShopEquipInterface.cpp) <br>
Find <br>

	if (!pWrapperGItem->GetHandleSell()) { return false; }

Replace <br>

	if (!pWrapperGItem->GetHandleSell()) { _ASSERT(0); return false; }

Find <br>

	if (!pWrappedZItem->GetHandleSell()) { return false; }

Replace <br>

	if (!pWrappedZItem->GetHandleSell()) { _ASSERT(0); return false; }

Find <br>

	bool ZShopEquipInterface::CheckRestrictBringAccountItem()
	{
		int nResult = _CheckRestrictBringAccountItem();
		if (nResult == 0 || nResult == 2)	// Restriction Passed
			return true;

		if (nResult == -1)
			ZGetGameInterface()->ShowErrorMessage(  MERR_NO_SELITEM );

		else if (nResult == 1)	// Sex Restrict
			ZGetGameInterface()->ShowErrorMessage( MERR_BRING_ACCOUNTITEM_BECAUSEOF_SEX );

		return false;
	}

Replace <br>

	bool ZShopEquipInterface::CheckRestrictBringAccountItem()
	{
		int nResult = _CheckRestrictBringAccountItem();
		if (nResult == 0 || nResult == 2)	// Restriction Passed
			return true;

		if (nResult == -1)
			ZGetGameInterface()->ShowErrorMessage(  MERR_NO_SELITEM );

		else if (nResult == 1)	// Sex Restrict
			ZGetGameInterface()->ShowErrorMessage( MERR_BRING_ACCOUNTITEM_BECAUSEOF_SEX );

		else
			_ASSERT(FALSE);	// Unknown Restriction

		return false;
	}

Open(ZShop.cpp) <br>
Find <br>

	if (!pListBox) { return; }

Replace <br>

	if (!pListBox) { _ASSERT(0); return; }

Find <br>

	if (pDesc == NULL) {  continue; }

Replace <br>

	if (pDesc == NULL) { _ASSERT(0); continue; }


Open(ZOptionInterface.cpp) <br>

Find <br>

			D3DDISPLAYMODE ddm;
			ddm.Width = 640;
			ddm.Height = 480;
			ddm.Format = D3DFMT_R5G6B5;


Replace <br>

			D3DDISPLAYMODE ddm;
			ddm.Width = 800;//640;
			ddm.Height = 600;//480;
			ddm.Format = D3DFMT_R5G6B5;


Open(ZGameInterface_OnCommand.cpp) <br>
Find <br>

	case MC_MATCH_DUELTOURNAMENT_RESPONSE_SIDERANKING_INFO:
		{
			MCommandParameter* pParam = pCommand->GetParameter(0);
			if (!pParam) {  break; }
			void* pBlob = pParam->GetPointer();
			int nCount = MGetBlobArrayCount( pBlob);
			
			//_ASSERT(nCount <= NUM_DISPLAY_DUELTOURNAMENT_RANKING);

			ZIDLResource* pRes = ZApplication::GetGameInterface()->GetIDLResource();
			MWidget* pWidget = pRes->FindWidget( "Lobby_DuelTournamentRankingList" );
			if (!pWidget) {  break; }

			ZDuelTournamentRankingListBox* pRankingList = (ZDuelTournamentRankingListBox*)pWidget;
			pRankingList->ClearAll();

			for (int i=0; i<nCount; ++i)
			{
				DTRankingInfo *pRankInfo = reinterpret_cast<DTRankingInfo*>(MGetBlobArrayElement(pBlob, i));
				if (!pRankInfo) {  break; }
				
				ZDUELTOURNAMENTRANKINGITEM tempItem;
				tempItem.bEmptyItem		= false;
				tempItem.nFluctuation	= pRankInfo->m_nRankingIncrease;
				tempItem.nLosses		= pRankInfo->m_nLoses;
				tempItem.nWins			= pRankInfo->m_nWins;
				tempItem.nWinners		= pRankInfo->m_nFinalWins;
				tempItem.nPoint			= pRankInfo->m_nTP;
				tempItem.nRank			= pRankInfo->m_nRanking;
				tempItem.nGrade			= pRankInfo->m_nGrade;
				strcpy(tempItem.szCharName, pRankInfo->m_szCharName);

				// DB¿¡¼­ ¹«È¿°ªÀ¸·Î 10¾ïÀ» »ç¿ëÇÑ´Ù, ¿©±â¼­´Â º¯µ¿¾øÀ½À¸·Î Ãâ·Â
				if(tempItem.nFluctuation == 1000000000)
					tempItem.nFluctuation = 0;

				pRankingList->SetRankInfo(i, tempItem);
			}

			// ´ÙÀ½ ¼øÀ§±îÁö ³²Àº Æ÷ÀÎÆ® Ãâ·Â
			int myRankIndex = -1;
			for (int i=0; i<nCount; ++i)
			{
				DTRankingInfo *pRankInfo = reinterpret_cast<DTRankingInfo*>(MGetBlobArrayElement(pBlob, i));
				if (!pRankInfo) {  break; }

				if (0 == strcmp(pRankInfo->m_szCharName, ZGetMyInfo()->GetCharName())) {
					myRankIndex = i;
					break;
				}
			}

			pRankingList->SetMyRankIndex(myRankIndex);

			DTRankingInfo *pMyRankInfo = reinterpret_cast<DTRankingInfo*>(MGetBlobArrayElement(pBlob, myRankIndex));
			DTRankingInfo *pCloseRankInfo = reinterpret_cast<DTRankingInfo*>(MGetBlobArrayElement(pBlob, myRankIndex - 1));

			// '´ÙÀ½ ¼øÀ§±îÁö xxÆ÷ÀÎÆ® ³²¾Ò½À´Ï´Ù' Ãâ·Â
			pWidget = pRes->FindWidget("Lobby_DuelTournamentNeedPointNextRank");
			if (pWidget)
			{
				char szNeedPointToNextRank[32] = "";

				char szBuffer[256] = "";
				if (pMyRankInfo && pCloseRankInfo)
				{
					sprintf(szNeedPointToNextRank, "%d", pCloseRankInfo->m_nTP - pMyRankInfo->m_nTP);
					ZTransMsg(szBuffer, MSG_LOBBY_DUELTOURNAMENT_NEEDPOINT_TONEXTRANK, 1, szNeedPointToNextRank);
				}
				else
				{
					// ³»°¡ 1µîÀÌ¶ó¼­ ³» »óÀ§ ·©Ä¿°¡ ¾ø°Å³ª, ³»°¡ ÀÌ¹øÁÖ¿¡ Ã³À½ Åä³Ê¸ÕÆ®¸¦ ÇÏ´Â°Å¶ó ·©Å·¿¡ ¾øÀ» °æ¿ì
					// ÀÌ ¸ñ·ÏÀÌ »óÀ§ 1~5µîÀ» ³ªÅ¸³»°í ÀÖÀ½À» ¼³¸í
					ZTransMsg(szBuffer, MSG_LOBBY_DUELTOURNAMENT_NEEDPOINT_TOPSIDERANKLIST);
				}

				pWidget->SetText(szBuffer);
				((MLabel*)pWidget)->SetAlignment(MAM_RIGHT);
			}
		}
		break;

Replace <br>

	case MC_MATCH_DUELTOURNAMENT_RESPONSE_SIDERANKING_INFO:
		{
			MCommandParameter* pParam = pCommand->GetParameter(0);
			if (!pParam) { _ASSERT(0); break; }
			void* pBlob = pParam->GetPointer();
			int nCount = MGetBlobArrayCount( pBlob);
			
			_ASSERT(nCount <= NUM_DISPLAY_DUELTOURNAMENT_RANKING);

			ZIDLResource* pRes = ZApplication::GetGameInterface()->GetIDLResource();
			MWidget* pWidget = pRes->FindWidget( "Lobby_DuelTournamentRankingList" );
			if (!pWidget) { _ASSERT(0); break; }

			ZDuelTournamentRankingListBox* pRankingList = (ZDuelTournamentRankingListBox*)pWidget;
			pRankingList->ClearAll();

			for (int i=0; i<nCount; ++i)
			{
				DTRankingInfo *pRankInfo = reinterpret_cast<DTRankingInfo*>(MGetBlobArrayElement(pBlob, i));
				if (!pRankInfo) { _ASSERT(0); break; }
				
				ZDUELTOURNAMENTRANKINGITEM tempItem;
				tempItem.bEmptyItem		= false;
				tempItem.nFluctuation	= pRankInfo->m_nRankingIncrease;
				tempItem.nLosses		= pRankInfo->m_nLoses;
				tempItem.nWins			= pRankInfo->m_nWins;
				tempItem.nWinners		= pRankInfo->m_nFinalWins;
				tempItem.nPoint			= pRankInfo->m_nTP;
				tempItem.nRank			= pRankInfo->m_nRanking;
				tempItem.nGrade			= pRankInfo->m_nGrade;
				strcpy(tempItem.szCharName, pRankInfo->m_szCharName);

				// DB¿¡¼­ ¹«È¿°ªÀ¸·Î 10¾ïÀ» »ç¿ëÇÑ´Ù, ¿©±â¼­´Â º¯µ¿¾øÀ½À¸·Î Ãâ·Â
				if(tempItem.nFluctuation == 1000000000)
					tempItem.nFluctuation = 0;

				pRankingList->SetRankInfo(i, tempItem);
			}

			// ´ÙÀ½ ¼øÀ§±îÁö ³²Àº Æ÷ÀÎÆ® Ãâ·Â
			int myRankIndex = -1;
			for (int i=0; i<nCount; ++i)
			{
				DTRankingInfo *pRankInfo = reinterpret_cast<DTRankingInfo*>(MGetBlobArrayElement(pBlob, i));
				if (!pRankInfo) { _ASSERT(0); break; }

				if (0 == strcmp(pRankInfo->m_szCharName, ZGetMyInfo()->GetCharName())) {
					myRankIndex = i;
					break;
				}
			}

			pRankingList->SetMyRankIndex(myRankIndex);

			DTRankingInfo *pMyRankInfo = reinterpret_cast<DTRankingInfo*>(MGetBlobArrayElement(pBlob, myRankIndex));
			DTRankingInfo *pCloseRankInfo = reinterpret_cast<DTRankingInfo*>(MGetBlobArrayElement(pBlob, myRankIndex - 1));

			// '´ÙÀ½ ¼øÀ§±îÁö xxÆ÷ÀÎÆ® ³²¾Ò½À´Ï´Ù' Ãâ·Â
			pWidget = pRes->FindWidget("Lobby_DuelTournamentNeedPointNextRank");
			if (pWidget)
			{
				char szNeedPointToNextRank[32] = "";

				char szBuffer[256] = "";
				if (pMyRankInfo && pCloseRankInfo)
				{
					sprintf(szNeedPointToNextRank, "%d", pCloseRankInfo->m_nTP - pMyRankInfo->m_nTP);
					ZTransMsg(szBuffer, MSG_LOBBY_DUELTOURNAMENT_NEEDPOINT_TONEXTRANK, 1, szNeedPointToNextRank);
				}
				else
				{
					// ³»°¡ 1µîÀÌ¶ó¼­ ³» »óÀ§ ·©Ä¿°¡ ¾ø°Å³ª, ³»°¡ ÀÌ¹øÁÖ¿¡ Ã³À½ Åä³Ê¸ÕÆ®¸¦ ÇÏ´Â°Å¶ó ·©Å·¿¡ ¾øÀ» °æ¿ì
					// ÀÌ ¸ñ·ÏÀÌ »óÀ§ 1~5µîÀ» ³ªÅ¸³»°í ÀÖÀ½À» ¼³¸í
					ZTransMsg(szBuffer, MSG_LOBBY_DUELTOURNAMENT_NEEDPOINT_TOPSIDERANKLIST);
				}

				pWidget->SetText(szBuffer);
				((MLabel*)pWidget)->SetAlignment(MAM_RIGHT);
			}
		}
		break;

Open(ZMyCharacter.cpp) <br>
Find <br>

	if (zStatus.m_bSlash)

Replace <br>

		if (zStatus.m_bSlash)
		{
			if (GetItems()->GetItem(MMCIP_MELEE) == NULL)
			{
				return 1.f;
			}

			MMatchItemDesc *pDesc = GetItems()->GetItem(MMCIP_MELEE)->GetDesc();

			if (pDesc != NULL && pDesc->m_nWeaponType.Ref() == MWT_DOUBLE_KATANA) {
				AniFrameInfo* pAniLow = m_pVMesh->GetFrameInfo(ani_mode_lower);
				if (pAniLow->m_nFrame < 160 * 11) return 0;
			}
		}

		// ½Ö°Ë Æ¯¼öµ¿ÀÛ Á¡ÇÁ ¸ð¼Ç
		if (zStatus.m_bSkill) 
		{
			if (GetItems()->GetItem(MMCIP_MELEE) == NULL)
			{
				return 1.f;
			}

			MMatchItemDesc *pDesc = GetItems()->GetItem(MMCIP_MELEE)->GetDesc();

			if (pDesc != NULL && pDesc->m_nWeaponType.Ref() == MWT_DOUBLE_KATANA) {
				AniFrameInfo* pAniLow = m_pVMesh->GetFrameInfo(ani_mode_lower);
				if (pAniLow->m_nFrame < 160 * 20) return 0;
			}
		}

		return 1.f;
	}

Find <br>

	void ZMyCharacter::OnDamaged(ZObject* pAttacker, rvector srcPos, ZDAMAGETYPE damageType, MMatchWeaponType weaponType, float fDamage, float fPiercingRatio, int nMeleeType)

Add <br>

	void ZMyCharacter::OnDamaged(ZObject* pAttacker, rvector srcPos, ZDAMAGETYPE damageType, MMatchWeaponType weaponType, float fDamage, float fPiercingRatio, int nMeleeType)
	{
		if (!pAttacker) return;


Open(RealSpace2.cpp) <br>
Find <br>

	int nSettingAA = 2;
	
Replace

	//int nSettingAA = 2;

Find <br>

	ZeroMemory( &g_d3dpp, sizeof(g_d3dpp) );
	g_d3dpp.SwapEffect = D3DSWAPEFFECT_DISCARD;

Replace <br>

	ZeroMemory( &g_d3dpp, sizeof(g_d3dpp) );
	g_d3dpp.SwapEffect = D3DSWAPEFFECT_DISCARD;
	g_d3dpp.BackBufferWidth = params->nWidth;
	g_d3dpp.BackBufferHeight = params->nHeight;
	g_d3dpp.BackBufferCount = bTripleBuffer ? 2 : 1;						// or 2 ??
	g_d3dpp.Windowed   = !params->bFullScreen;
	g_d3dpp.BackBufferFormat = g_PixelFormat;
	g_d3dpp.EnableAutoDepthStencil = TRUE;

	/*	
		if( SUCCEEDED(g_pD3D->CheckDeviceMultiSampleType( D3DADAPTER_DEFAULT, D3DDEVTYPE_HAL , g_PixelFormat, params->bFullScreen, D3DMULTISAMPLE_4_SAMPLES, NULL ))) {
			g_MultiSample = D3DMULTISAMPLE_4_SAMPLES;
		}	
		else if( SUCCEEDED(g_pD3D->CheckDeviceMultiSampleType( D3DADAPTER_DEFAULT, D3DDEVTYPE_HAL , g_PixelFormat, params->bFullScreen, D3DMULTISAMPLE_2_SAMPLES, NULL ))) {
			g_MultiSample = D3DMULTISAMPLE_2_SAMPLES;
		}
		else 
			g_MultiSample = D3DMULTISAMPLE_NONE;

		mlog("sample %d \n", int(g_MultiSample));
	*/
		g_d3dpp.MultiSampleType =  g_MultiSample;
	//	g_d3dpp.MultiSampleType =  D3DMULTISAMPLE_NONE;


Find <br>

	g_bFullScreen=params->bFullScreen;
	g_nScreenWidth=params->nWidth;

Replace <br>

	g_bFullScreen=params->bFullScreen;
	g_nScreenWidth=params->nWidth;
	g_nScreenHeight=params->nHeight;
	g_PixelFormat= params->bFullScreen ? params->PixelFormat : g_d3ddm.Format;		// À©µµ¿ì ¸ðµå´Â ¿ø·¡ Æ÷¸ËÀ¸·Î

	g_d3dpp.Windowed   = !params->bFullScreen;
 	g_d3dpp.BackBufferWidth = g_nScreenWidth;
 	g_d3dpp.BackBufferHeight = g_nScreenHeight;
	g_d3dpp.BackBufferFormat = g_PixelFormat;
	/*
		if( SUCCEEDED(g_pD3D->CheckDeviceMultiSampleType( D3DADAPTER_DEFAULT, D3DDEVTYPE_HAL , g_PixelFormat, params->bFullScreen, D3DMULTISAMPLE_4_SAMPLES, NULL ))) {
			g_MultiSample = D3DMULTISAMPLE_4_SAMPLES;
		}	
		else if( SUCCEEDED(g_pD3D->CheckDeviceMultiSampleType( D3DADAPTER_DEFAULT, D3DDEVTYPE_HAL , g_PixelFormat, params->bFullScreen, D3DMULTISAMPLE_2_SAMPLES, NULL ))) {
			g_MultiSample = D3DMULTISAMPLE_2_SAMPLES;
		}
		else 
			g_MultiSample = D3DMULTISAMPLE_NONE;

		mlog("sample %d \n", int(g_MultiSample));
	*/
		g_d3dpp.MultiSampleType =  g_MultiSample;
	//	g_d3dpp.MultiSampleType =  D3DMULTISAMPLE_NONE;

Find <br>

	void ChangeAA(int AALevel)
	{
		nSettingAA = AALevel;
	}

Replace <br>

	//void ChangeAA(int AALevel)
	//{
	//	nSettingAA = AALevel;
	//}

Open(main.cpp)

	ChangeAA(ZGetConfiguration()->GetVideo()->nAntiAlias);

Replace

	//ChangeAA(ZGetConfiguration()->GetVideo()->nAntiAlias);

Open(ZOptionInterface.cpp) <br>
Find <br>

		pWidget = (MComboBox*)pResource->FindWidget("AntiAlias");

		if(pWidget)	{

			int AntiAlias = pWidget->GetSelIndex();

			if( ZGetConfiguration()->GetVideo()->nAntiAlias != AntiAlias ) {
				ZGetConfiguration()->GetVideo()->nAntiAlias = AntiAlias;
				ChangeAA(AntiAlias);
				RMODEPARAMS ModeParams={ RGetScreenWidth(),RGetScreenHeight(),RIsFullScreen(),RGetPixelFormat() };
				RResetDevice(&ModeParams);
	
			}
		}

Replace <br>

		//pWidget = (MComboBox*)pResource->FindWidget("AntiAlias");

		//if(pWidget)	{

		//	int AntiAlias = pWidget->GetSelIndex();

		//	if( ZGetConfiguration()->GetVideo()->nAntiAlias != AntiAlias ) {
		//		ZGetConfiguration()->GetVideo()->nAntiAlias = AntiAlias;
		//		ChangeAA(AntiAlias);
		//		RMODEPARAMS ModeParams={ RGetScreenWidth(),RGetScreenHeight(),RIsFullScreen(),RGetPixelFormat() };
		//		RResetDevice(&ModeParams);
	
		//	}
		//}

Open(RealSpace2.h) <br>
Find <br>

	void ChangeAA(int AALevel);

Replace <br>

	//void ChangeAA(int AALevel);


Open(ZActor.cpp) <br>
Find <br>

	void ZActor::Skill(int nSkill)

Replace <br>

	void ZActor::Skill(int nSkill)
	{
		ZSkillDesc *pDesc = m_pModule_Skills->GetSkill(nSkill)->GetDesc();
		if(pDesc) {
			if( pDesc->nCastingAnimation == 1 )
				m_Animation.Input(ZA_EVENT_SPECIAL1);
			else if (pDesc->nCastingAnimation == 2 )
				m_Animation.Input(ZA_EVENT_SPECIAL2);
			else if (pDesc->nCastingAnimation == 3 )
				m_Animation.Input(ZA_EVENT_SPECIAL3);
			else if (pDesc->nCastingAnimation == 4 )
				m_Animation.Input(ZA_EVENT_SPECIAL4);
			else if (pDesc->nCastingAnimation == 0 )
				return;


			else { _ASSERT(FALSE); }
		}
	}


Open(ZShopEquipItemConfirm.cpp)
Find <br>

	bool ZSimpleConfirmDlg::OnCommand(MWidget* pWidget, const char* szMessage)
	{

Replace <br>

	bool ZSimpleConfirmDlg::OnCommand(MWidget* pWidget, const char* szMessage)
	{
		if (pWidget == m_pMsgbox)
		{
			if (strcmp(szMessage, MMSGBOX_OK)==0)
			{
				m_pMsgbox->Show(false);
				if (!m_pDoneHandler) { _ASSERT(0); return true; }
				m_pDoneHandler->OnDone(true);
			}
			else if (strcmp(szMessage, MMSGBOX_CANCEL)==0)
			{
				m_pMsgbox->Show(false);
				if (!m_pDoneHandler) { _ASSERT(0); return true; }
				m_pDoneHandler->OnDone(false);
			}
		}
		return true;
	}

Open(ZLocale.cpp) <br>
Find <br>

	void ZLocale::SetTeenMode(bool bTeenMode)

Replace <br>

	void ZLocale::SetTeenMode(bool bTeenMode)
	{
		if (m_pAuthInfo) m_pAuthInfo->SetTeenMode(bTeenMode);
		else _ASSERT(0);
	}

Open(ZGameAction.cpp) <br>
Find OnPeerSkill_Dash <br>

	ZItem *pItem = pOwnerCharacter->GetItems()->GetItem(MMCIP_MELEE);
	if(!pItem) return;
	MMatchItemDesc *pDesc = pItem->GetDesc();
	if(!pDesc) { return; }

Replace <br>

	ZItem *pItem = pOwnerCharacter->GetItems()->GetItem(MMCIP_MELEE);
	if(!pItem) return;
	MMatchItemDesc *pDesc = pItem->GetDesc();
	if(!pDesc) { _ASSERT(FALSE); return; }

Open(ZGame.cpp) <br>
Find <br>

	char tmpbuf[128];
	_strtime( tmpbuf );

Add above <br>

	ZGetCharacterManager()->Clear();


Find <br>

	void ZGame::ApplyPotion(int nItemID, ZCharacter* pCharObj, float fRemainedTime)
	{
		MMatchItemDesc* pDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);
		if( pDesc == NULL ) { return; }

Change <br>

	void ZGame::ApplyPotion(int nItemID, ZCharacter* pCharObj, float fRemainedTime)
	{
		MMatchItemDesc* pDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);
		if( pDesc == NULL ) { _ASSERT(0);  return; }


Find <br>

	else if (nDamageType == MMDT_HEAL || nDamageType == MMDT_REPAIR)
	{
		// Áï½Ã È¸º¹ ¾ÆÀÌÅÛ
		if (pDesc->m_nDamageTime.Ref() == 0)
		{
			ZGetEffectManager()->AddPotionEffect( pCharObj->GetPosition(), pCharObj, pDesc->m_nEffectId );

			if (nDamageType == MMDT_HEAL)
			{
				int nAddedHP = pDesc->m_nItemPower.Ref();
				pCharObj->SetHP( min( pCharObj->GetHP() + nAddedHP, pCharObj->GetMaxHP() ) );
			}
			else if (nDamageType == MMDT_REPAIR)
			{
				int nAddedAP = pDesc->m_nItemPower.Ref();
				pCharObj->SetAP( min( pCharObj->GetAP() + nAddedAP, pCharObj->GetMaxAP() ) );
			}
		}

Replace <br>

	else if (nDamageType == MMDT_HEAL || nDamageType == MMDT_REPAIR)
	{
		// БпЅГ Иёє№ ѕЖАМЕЫ
		if (pDesc->m_nDamageTime.Ref() == 0)
		{
			ZGetEffectManager()->AddPotionEffect( pCharObj->GetPosition(), pCharObj, pDesc->m_nEffectId );

			if (nDamageType == MMDT_HEAL)
			{
				int nAddedHP = pDesc->m_nItemPower.Ref();
				pCharObj->SetHP( min( pCharObj->GetHP() + nAddedHP, pCharObj->GetMaxHP() ) );
			}
			else if (nDamageType == MMDT_REPAIR)
			{
				int nAddedAP = pDesc->m_nItemPower.Ref();
				pCharObj->SetAP( min( pCharObj->GetAP() + nAddedAP, pCharObj->GetMaxAP() ) );
			}
			else
				_ASSERT(0);
		}

Find <br>

				switch (nDamageType)
				{
				case MMDT_HEAL:
					ZGetEffectManager()->AddHealOverTimeBeginEffect(pCharObj->GetPosition(), pCharObj);
					break;
				case MMDT_REPAIR:
					ZGetEffectManager()->AddRepairOverTimeBeginEffect(pCharObj->GetPosition(), pCharObj);
					break;
				}
			}
		}
	}


Change <br>

				switch (nDamageType)
				{
				case MMDT_HEAL:
					ZGetEffectManager()->AddHealOverTimeBeginEffect(pCharObj->GetPosition(), pCharObj);
					break;
				case MMDT_REPAIR:
					ZGetEffectManager()->AddRepairOverTimeBeginEffect(pCharObj->GetPosition(), pCharObj);
					break;
				}
			}
		}
		else
			_ASSERT(0);
	}

Find <br>

	void ZGame::OnUseTrap(int nItemID, ZCharacter* pCharObj, rvector& pos)
	{
		MMatchItemDesc* pDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);
		if( pDesc == NULL ) { return; }

		rvector velocity;
		velocity	= /*pCharObj->GetVelocity()+ */pCharObj->m_TargetDir * 1300.f;
		velocity.z  = velocity.z + 300.f;
		m_WeaponManager.AddTrap(pos, velocity, nItemID, pCharObj);
	}

Change <br>

	void ZGame::OnUseTrap(int nItemID, ZCharacter* pCharObj, rvector& pos)
	{
		MMatchItemDesc* pDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);
		if( pDesc == NULL ) { _ASSERT(0); return; }

		rvector velocity;
		velocity	= /*pCharObj->GetVelocity()+ */pCharObj->m_TargetDir * 1300.f;
		velocity.z  = velocity.z + 300.f;
		m_WeaponManager.AddTrap(pos, velocity, nItemID, pCharObj);
	}

Find <br>

	void ZGame::OnUseDynamite(int nItemID, ZCharacter* pCharObj, rvector& pos)
	{
		MMatchItemDesc* pDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);
		if( pDesc == NULL ) { return; }

		rvector velocity;
		velocity	= /*pCharObj->GetVelocity()+ */pCharObj->m_TargetDir * 1300.f;
		velocity.z  = velocity.z + 300.f;
		m_WeaponManager.AddDynamite(pos, velocity, pCharObj);
	}

Change <br>

	void ZGame::OnUseDynamite(int nItemID, ZCharacter* pCharObj, rvector& pos)
	{
		MMatchItemDesc* pDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);
		if( pDesc == NULL ) { _ASSERT(0); return; }

		rvector velocity;
		velocity	= /*pCharObj->GetVelocity()+ */pCharObj->m_TargetDir * 1300.f;
		velocity.z  = velocity.z + 300.f;
		m_WeaponManager.AddDynamite(pos, velocity, pCharObj);
	}

Find <br>

		if (m_pGameAction)
		{
			int nDuration = pItemDesc->m_nDamageTime.Ref();
			bool bApplied = false;
			switch (pItemDesc->m_nDamageType.Ref())
			{
			case MMDT_FIRE:
				// ºÒÆ®·¦Àº ItemPower°¡ ´ë¹ÌÁö¸¦ ¶æÇÔ
				bApplied = m_pGameAction->ApplyFireEnchantDamage(pTarget, pOwnerCharacter, pItemDesc->m_nItemPower.Ref(), nDuration);
				break;
			case MMDT_COLD:
				// ¾óÀ½Æ®·¦Àº ItemPower°¡ ÀÌ¼Ó°¨¼Ò·®À» ¶æÇÔ (80ÀÌ¸é 80%ÀÇ ¼Ó·ÂÀÌ µÊ)
				bApplied = m_pGameAction->ApplyColdEnchantDamage(pTarget, pItemDesc->m_nItemPower.Ref(), nDuration);
				break;
			default:
				{
				break;
				}
			}

Change <br>

		if (m_pGameAction)
		{
			int nDuration = pItemDesc->m_nDamageTime.Ref();
			bool bApplied = false;
			switch (pItemDesc->m_nDamageType.Ref())
			{
			case MMDT_FIRE:
				// єТЖ®·¦Ає ItemPower°Ў ґл№МБцё¦ ¶жЗФ
				bApplied = m_pGameAction->ApplyFireEnchantDamage(pTarget, pOwnerCharacter, pItemDesc->m_nItemPower.Ref(), nDuration);
				break;
			case MMDT_COLD:
				// ѕуАЅЖ®·¦Ає ItemPower°Ў АМјУ°ЁјТ·®А» ¶жЗФ (80АМёй 80%АЗ јУ·ВАМ µК)
				bApplied = m_pGameAction->ApplyColdEnchantDamage(pTarget, pItemDesc->m_nItemPower.Ref(), nDuration);
				break;
			default:
				_ASSERT(0);
			}

Open(ZGame.cpp) <br>
Find <br>

	void ZGame::ProcessDelayedCommand()
	{
		for(ZObserverCommandList::iterator i = m_DelayedCommandList.begin(); i != m_DelayedCommandList.end();i++)
		{
			ZObserverCommandItem *pItem = *i;

			// ½ÇÇàÇÒ ½Ã°£ÀÌ Áö³µÀ¸¸é ½ÇÇàÇÑ´Ù
			if(GetTime() > pItem->fTime) 
			{
				OnCommand_Immidiate(pItem->pCommand);
				i = m_DelayedCommandList.erase(i);
				delete pItem->pCommand;
				delete pItem;
			}
		}
	}

Change <br>

	void ZGame::ProcessDelayedCommand()
	{
		//Iterator increment fix
		for(ZObserverCommandList::iterator i = m_DelayedCommandList.begin(); i != m_DelayedCommandList.end();)
		{
			ZObserverCommandItem *pItem = *i;

			// ½ÇÇàÇÒ ½Ã°£ÀÌ Áö³µÀ¸¸é ½ÇÇàÇÑ´Ù
			if(GetTime() > pItem->fTime) 
			{
				OnCommand_Immidiate(pItem->pCommand);
				i = m_DelayedCommandList.erase(i);
				delete pItem->pCommand;
				delete pItem;
			}
			else
				++i;
		}
	}

Find <br>

	int ZGame::SelectSlashEffectMotion(ZCharacter* pCharacter)
	{
		if(pCharacter==NULL) return SEM_None;


Replace <br>

	int ZGame::SelectSlashEffectMotion(ZCharacter* pCharacter)
	{
		//Exploit fix (pCharacter->GetSelectItemDesc() == NULL crash)
		if(pCharacter==NULL || pCharacter->GetSelectItemDesc() == NULL) return SEM_None;


Open(MMatchStage.cpp) <br>
Find <br>

	if (count == 0 && !MMATCH_GAMETYPE_DUEL)
	{
		pRelayMapList[0].nMapID = MMATCH_MAP_MANSION;
		count = 1;
	}else{
		pRelayMapList[0].nMapID = MMATCH_MAP_HALL;
		count = 1;
	}

Replace <br>

	if (count == 0)
	{
		pRelayMapList[0].nMapID = MMATCH_MAP_MANSION;
		count = 1;
	}

Find <br>

	nRelayMapListCount = 20;

Replace <br>

	nRelayMapListCount = 26;

Open(ZGameClient_OnCommand.cpp) <br>
Find <br>

				DWORD dwRet = _AhnHS_MakeAckMsg(pReqMsg, ZGetMyInfo()->GetSystemInfo()->pbyAckMsg);

				if(dwRet != ERROR_SUCCESS)
					mlog("Making Ack Msg Failed. (Error code = %x)\n", dwRet);

Replace <br>

				/*DWORD dwRet = _AhnHS_MakeAckMsg(pReqMsg, ZGetMyInfo()->GetSystemInfo()->pbyAckMsg);

				if(dwRet != ERROR_SUCCESS)
					mlog("Making Ack Msg Failed. (Error code = %x)\n", dwRet);*/


Open(MBaseChannelRule.cpp) <br>
Find <br>

	bool MChannelRuleMapList::Exist(int nMapID, bool bOnlyDuel)
	{
		set<int>::iterator itor = m_Set.find( nMapID);

		if ( itor != m_Set.end())
		{
			int id = (*itor);
			if ( !bOnlyDuel && MGetMapDescMgr()->IsMapOnlyDuel(id)) return false;
			if ( bOnlyDuel && !MGetMapDescMgr()->IsMapOnlyDuel(id)) return false;
			return true;
		}

		return false;
	}

Replace <br>

	bool MChannelRuleMapList::Exist(int nMapID, bool bOnlyDuel)
	{
		set<int>::iterator itor = m_Set.find( nMapID);

		if ( itor != m_Set.end())
		{
			int id = (*itor);

			if ( !bOnlyDuel && MGetMapDescMgr()->IsMapOnlyDuel(id))
				return false;

			return true;
		}

		return false;
	}

Find <br>

	bool MChannelRuleMapList::Exist(const char* pszMapName, bool bOnlyDuel)
	{
		for (set<int>::iterator itor = m_Set.begin(); itor != m_Set.end(); ++itor)
		{
			int id = (*itor);

			if ((id >= 0) && (id < MMATCH_MAP_MAX))
			{
				if ( !stricmp(pszMapName, MGetMapDescMgr()->GetMapName(id)) )
				{
					if ( !bOnlyDuel && MGetMapDescMgr()->IsMapOnlyDuel(id)) return false;
					if ( bOnlyDuel && !MGetMapDescMgr()->IsMapOnlyDuel(id)) return false;
					return true;
				}
			}
		}

		return false;
	}

Replace <br>

	bool MChannelRuleMapList::Exist(const char* pszMapName, bool bOnlyDuel)
	{
		for (set<int>::iterator itor = m_Set.begin(); itor != m_Set.end(); ++itor)
		{
			int id = (*itor);

			if ((id >= 0) && (id < MMATCH_MAP_MAX))
			{
				if ( !stricmp(pszMapName, MGetMapDescMgr()->GetMapName(id)) )
				{
					if ( !bOnlyDuel && MGetMapDescMgr()->IsMapOnlyDuel(id))
						return false;

					return true;
				}
			}
		}

		return false;
	}

Open(MMatchConfig.cpp) <br>
Find <br>

	if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_NORMAL)) m_nServerMode = MSM_NORMALS;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_CLAN)) m_nServerMode = MSM_CLAN;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_LADDER)) m_nServerMode = MSM_LADDER;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_EVENT)) m_nServerMode = MSM_EVENT;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_TEST)) m_nServerMode = MSM_CLAN;

	m_bEnabledSurvivalMode = (0 != GetPrivateProfileInt("SERVER", "SURVIVALENABLE", 1, SERVER_CONFIG_FILENAME));

Replace <br>

	if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_NORMAL)) m_nServerMode = MSM_NORMALS;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_CLAN)) m_nServerMode = MSM_CLAN;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_LADDER)) m_nServerMode = MSM_LADDER;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_EVENT)) m_nServerMode = MSM_EVENT;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_TEST)) m_nServerMode = MSM_CLAN;
	else { _ASSERT(0); }

	m_bEnabledSurvivalMode = (0 != GetPrivateProfileInt("SERVER", "SURVIVALENABLE", 1, SERVER_CONFIG_FILENAME));

Open(MMatchMap.cpp) <br> 
Find <br>

		for (int i = 0; i < iCount; i++)
		{
			aChild = aParent.GetChildNode(i);
			aChild.GetTagName(szTagName);
			if(stricmp(szTagName,MMAP_MAP)==0)
			{
				if (szTagName[0] == '#') continue;

				//_ASSERT( 0 <= num && num < MMATCH_MAP_COUNT);

				aChild.GetAttribute(&m_MapVectors[num].nMapID, MMAP_ID);
				aChild.GetAttribute(m_MapVectors[num].szMapName , MMAP_NAME);
				aChild.GetAttribute(m_MapVectors[num].szMapImageName , MMAP_IMAGENAME);
				aChild.GetAttribute(m_MapVectors[num].szBannerName , MMAP_BANNERNAME);
				aChild.GetAttribute(&m_MapVectors[num].fExpRatio , MMAP_EXPRATIO);
				aChild.GetAttribute(&m_MapVectors[num].nMaxPlayers , MMAP_MAXPLAYERS);
				aChild.GetAttribute(&m_MapVectors[num].bOnlyDuelMap , MMAP_ONLYDUELMAP);
				aChild.GetAttribute(&m_MapVectors[num].bIsCTFMap , MMAP_ISCTFMAP);

				num++;
			}
		}

		return true;
	}

Replace <br>

		for (int i = 0; i < iCount; i++)
		{
			aChild = aParent.GetChildNode(i);
			aChild.GetTagName(szTagName);
			if(stricmp(szTagName,MMAP_MAP)==0)
			{
				if (szTagName[0] == '#') continue;

				_ASSERT( 0 <= num && num < MMATCH_MAP_COUNT);

				if (num < 0 || num >= MMATCH_MAP_COUNT)
					return false;

				aChild.GetAttribute(&m_MapVectors[num].nMapID, MMAP_ID);
				aChild.GetAttribute(m_MapVectors[num].szMapName , MMAP_NAME);
				aChild.GetAttribute(m_MapVectors[num].szMapImageName , MMAP_IMAGENAME);
				aChild.GetAttribute(m_MapVectors[num].szBannerName , MMAP_BANNERNAME);
				aChild.GetAttribute(&m_MapVectors[num].fExpRatio , MMAP_EXPRATIO);
				aChild.GetAttribute(&m_MapVectors[num].nMaxPlayers , MMAP_MAXPLAYERS);
				aChild.GetAttribute(&m_MapVectors[num].bOnlyDuelMap , MMAP_ONLYDUELMAP);
				aChild.GetAttribute(&m_MapVectors[num].bIsCTFMap, MMAP_ISCTFMAP);
				num++;
			}
		}

		return true;
	}

Find <br>

	for (int i = 0; i < iCount; i++)
	{
		aChild = aParent.GetChildNode(i);
		aChild.GetTagName(szTagName);
		if(stricmp(szTagName,MMAP_MAP)==0)
		{
			if (szTagName[0] == '#') continue;

			if (num >= MMATCH_MAP_COUNT)
			{
				_ASSERT(0);
				return false;
			}
			
			aChild.GetAttribute(&m_MapVectors[num].nMapID, MMAP_ID);
			aChild.GetAttribute(m_MapVectors[num].szMapName , MMAP_NAME);
			aChild.GetAttribute(m_MapVectors[num].szMapImageName , MMAP_IMAGENAME);
			aChild.GetAttribute(m_MapVectors[num].szBannerName , MMAP_BANNERNAME);
			aChild.GetAttribute(&m_MapVectors[num].fExpRatio , MMAP_EXPRATIO);
			aChild.GetAttribute(&m_MapVectors[num].nMaxPlayers , MMAP_MAXPLAYERS);
			aChild.GetAttribute(&m_MapVectors[num].bOnlyDuelMap , MMAP_ONLYDUELMAP);
			aChild.GetAttribute(&m_MapVectors[num].bIsCTFMap, MMAP_ISCTFMAP);		
			num++;
		}
	}
    
	if(buffer)
	{
		delete[] buffer;
		buffer = NULL; 
	}


Replace <br>

	for (int i = 0; i < iCount; i++)
	{
		aChild = aParent.GetChildNode(i);
		aChild.GetTagName(szTagName);
		if(stricmp(szTagName,MMAP_MAP)==0)
		{
			if (szTagName[0] == '#') continue;

			if (num >= MMATCH_MAP_COUNT)
			{
				_ASSERT(0);
				return false;
			}
			
			aChild.GetAttribute(&m_MapVectors[num].nMapID, MMAP_ID);
			aChild.GetAttribute(m_MapVectors[num].szMapName , MMAP_NAME);
			aChild.GetAttribute(m_MapVectors[num].szMapImageName , MMAP_IMAGENAME);
			aChild.GetAttribute(m_MapVectors[num].szBannerName , MMAP_BANNERNAME);
			aChild.GetAttribute(&m_MapVectors[num].fExpRatio , MMAP_EXPRATIO);
			aChild.GetAttribute(&m_MapVectors[num].nMaxPlayers , MMAP_MAXPLAYERS);
			aChild.GetAttribute(&m_MapVectors[num].bOnlyDuelMap , MMAP_ONLYDUELMAP);
			aChild.GetAttribute(&m_MapVectors[num].bIsCTFMap, MMAP_ISCTFMAP);		
			num++;
		}
	}
    
	if(buffer)
	{
		delete[] buffer;
		buffer = NULL; 
	}

Open(MTeamGameStrategy.cpp) <br>
Find <br>

	MBaseTeamGameStrategy* MBaseTeamGameStrategy::GetInstance(MMatchServerMode nServerMode)
	{
		switch (nServerMode)
		{
		case MSM_LADDER:
			return MLadderGameStrategy::GetInstance();
		case MSM_CLAN:
			return MClanGameStrategy::GetInstance();
		default:
			{
			}
		}
		return NULL;
	}

Replace <br>

	MBaseTeamGameStrategy* MBaseTeamGameStrategy::GetInstance(MMatchServerMode nServerMode)
	{
		switch (nServerMode)
		{
		case MSM_LADDER:
			return MLadderGameStrategy::GetInstance();
		case MSM_CLAN:
			return MClanGameStrategy::GetInstance();
		default:
			_ASSERT(0);
		}
		return NULL;
	}

Open(OnProcessAsyncJob.cpp) <br>
Find <br>

	//	m_Monitor.OnPostAsyncCpuUsage(pJob->GetProtocolID(), pJob->GetTaskName(), uiCpuUsage);

Replace <br>

	m_Monitor.OnPostAsyncCpuUsage(pJob->GetProtocolID(), pJob->GetTaskName(), uiCpuUsage);

Open(ZStageInterface.cpp) <br>
Find <br>

	if( !xmlQuestItemDesc.LoadFromMemory( buffer))
	{
		xmlQuestItemDesc.Destroy();
		return false;
	}

Replace <br>

	if( !xmlQuestItemDesc.LoadFromMemory( buffer))
	{
		delete[] buffer;
		xmlQuestItemDesc.Destroy();
		return false;
	}

Open(MMatchConfig.cpp) <br>
Find <br>

	if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_NORMAL)) m_nServerMode = MSM_NORMALS;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_CLAN)) m_nServerMode = MSM_CLAN;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_LADDER)) m_nServerMode = MSM_LADDER;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_EVENT)) m_nServerMode = MSM_EVENT;
	else if (!stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_TEST)) m_nServerMode = MSM_TEST;
	else { _ASSERT(0); }

Replace <br>

	if (!_stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_NORMAL)) m_nServerMode = MSM_NORMALS;
	else if (!_stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_CLAN)) m_nServerMode = MSM_CLAN;
	else if (!_stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_LADDER)) m_nServerMode = MSM_LADDER;
	else if (!_stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_EVENT)) m_nServerMode = MSM_EVENT;
	else if (!_stricmp(szServerMode, SERVER_CONFIG_SERVERMODE_TEST)) m_nServerMode = MSM_TEST;
	else { _ASSERT(0); }

Find <br>

	if( 0 == stricmp("0", szDebug) )

Replace <br>

	if( 0 == _stricmp("0", szDebug) )

Find <br>

	if( 0 == stricmp("r", szNHNServerMode) )
	{
		m_NHNServerMode = NSM_REAL;
		//mlog( "nhn server mode is real\n" );
	}
	else if( 0 == stricmp("a", szNHNServerMode) )
	{
		m_NHNServerMode = NSM_ALPHA;
		//mlog( "nhn server mode is alpha\n" );
	}

Replace <br>

	if( 0 == _stricmp("r", szNHNServerMode) )
	{
		m_NHNServerMode = NSM_REAL;
		//mlog( "nhn server mode is real\n" );
	}
	else if( 0 == _stricmp("a", szNHNServerMode) )
	{
		m_NHNServerMode = NSM_ALPHA;
		//mlog( "nhn server mode is alpha\n" );
	}

Find <br>

	if( 0 == stricmp("1", szUseHShield) )

Replace <br>

	if( 0 == _stricmp("1", szUseHShield) )

Find <br>

	if( 0 == stricmp("1", szUseXTrap) )

Replace <br>

	if( 0 == _stricmp("1", szUseXTrap) )

Find <br>

	if( 0 == stricmp("1", szUseEvent) )

Replace <br>

	if( 0 == _stricmp("1", szUseEvent) )

Find <br>

	if( 0 == stricmp("1", szUseFileCrc) )

Replace <br>

	if( 0 == _stricmp("1", szUseFileCrc) )

Find <br>

	if( 0 == stricmp("1", szUseMD5) )

Replace <br>

	if( 0 == _stricmp("1", szUseMD5) )

Find <br>

	if( 0 == stricmp("1", szBlockFlooding) )

Replace <br>

	if( 0 == _stricmp("1", szBlockFlooding) )

Find <br>

	if( 0 == stricmp("1", szBlockHacking) )

Replace <br>

	if( 0 == _stricmp("1", szBlockHacking) )

Find <br>

	if( 0 == stricmp("1", szItemConsistency) )	m_bIsUseItemConsistency = true;
	else										m_bIsUseItemConsistency = false;

Replace <br>

	if( 0 == _stricmp("1", szItemConsistency) )	m_bIsUseItemConsistency = true;
	else										m_bIsUseItemConsistency = false;
	
Find <br>

	if( 0 == stricmp("1", szUseResourceCRC32CacheCheck) )

Replace <br>

	if( 0 == _stricmp("1", szUseResourceCRC32CacheCheck) )

Find <br>

	if (stricmp("1", szUseLoopLog) == 0)

Replace <br>

	if (_stricmp("1", szUseLoopLog) == 0)

Find <br>

			if (!stricmp(szInputMapName, MGetMapDescMgr()->GetMapName(i)))
			{
				nMapIndex = i;
				break;
			}


Replace <br>

			if (!_stricmp(szInputMapName, MGetMapDescMgr()->GetMapName(i)))
			{
				nMapIndex = i;
				break;
			}

Open(ZGame.cpp) <br>
Find <br>

	void ZGame::OnPeerBuffInfo(const MUID& uidSender, void* pBlobBuffInfo)

Replace <br>

	void ZGame::OnPeerBuffInfo(const MUID& uidSender, void* pBlobBuffInfo)
	{
		if (uidSender == ZGetMyUID()) return;

		ZCharacter* pSender = ZGetCharacterManager()->Find(uidSender);
		if (!pSender) return;
		if (!pBlobBuffInfo) return;

		MTD_BuffInfo* pBuffInfo = NULL;
		int numElem = MGetBlobArrayCount(pBlobBuffInfo);

		//Exploit fix (MTD_PeerBuffInfo blob overflow)
	    if (MGetBlobArraySize(pBlobBuffInfo) != (8 + (sizeof(MTD_BuffInfo) * numElem)))
		{
		return;
	    }

		for (int i=0; i<numElem; ++i)
		{
			pBuffInfo = (MTD_BuffInfo*)MGetBlobArrayElement(pBlobBuffInfo, i);

			ApplyPotion(pBuffInfo->nItemId, pSender, (float)pBuffInfo->nRemainedTime);
		}
	}

Find <br>

		else
		{
	// 		sprintf(szMsg, "%s´ÔÀÌ %s´ÔÀ¸·ÎºÎÅÍ ½Â¸®ÇÏ¿´½À´Ï´Ù.", szAttacker, szVictim );
			ZTransMsg( szMsg, MSG_GAME_WHO_WIN_FROM_OTHER, 2, szAttacker, szVictim );
			ZChatOutput(MCOLOR(0xFF707070), szMsg);

			// Admin Grade
			if (ZGetMyInfo()->IsAdminGrade()) {
				MMatchObjCache* pCache = ZGetGameClient()->FindObjCache(ZGetMyUID());
				if (pCache && pCache->CheckFlag(MTD_PlayerFlags_AdminHide))
				{
					sprintf( szMsg, "^%d%s^9 ½Â¸®,  ^%d%s^9 ÆÐ¹è",
								(pAttacker->GetTeamID() == MMT_BLUE) ? 3 : 1, pAttacker->GetProperty()->GetName(),
								(pVictim->GetTeamID() == MMT_BLUE) ? 3 : 1,   pVictim->GetProperty()->GetName());
					ZGetGameInterface()->GetCombatInterface()->m_AdminMsg.OutputChatMsg( szMsg);
				}
			}
		}
	}

Replace <br>

		else
		{
	 		//sprintf(szMsg, "%s´ÔÀÌ %s´ÔÀ¸·ÎºÎÅÍ ½Â¸®ÇÏ¿´½À´Ï´Ù.", szAttacker, szVictim );
			ZTransMsg( szMsg, MSG_GAME_WHO_WIN_FROM_OTHER, 2, szAttacker, szVictim );
			ZChatOutput(MCOLOR(0xFF707070), szMsg);

			// Admin Grade
			if (ZGetMyInfo()->IsAdminGrade()) {
				MMatchObjCache* pCache = ZGetGameClient()->FindObjCache(ZGetMyUID());
				if (pCache && pCache->CheckFlag(MTD_PlayerFlags_AdminHide))
				{
					//Changed string
					sprintf( szMsg, "^%d%s^9 wins,  ^%d%s^9 loses", //sprintf( szMsg, "^%d%s^9 ½Â¸®,  ^%d%s^9 ÆÐ¹è",
								(pAttacker->GetTeamID() == MMT_BLUE) ? 3 : 1, pAttacker->GetProperty()->GetName(),
								(pVictim->GetTeamID() == MMT_BLUE) ? 3 : 1,   pVictim->GetProperty()->GetName());
					ZGetGameInterface()->GetCombatInterface()->m_AdminMsg.OutputChatMsg( szMsg);
				}
			}
		}
	}

Find <br>

		else
		{
			//sprintf(szMsg, "%s´ÔÀÌ ½º½º·Î ÆÐ¹èÇÏ¿´½À´Ï´Ù.", szAttacker);
			ZTransMsg( szMsg, MSG_GAME_WHO_LOSE_SELF, 1, szAttacker );
			ZChatOutput(MCOLOR(0xFF707070), szMsg);

			// Admin Grade
			if (ZGetMyInfo()->IsAdminGrade()) {
				MMatchObjCache* pCache = ZGetGameClient()->FindObjCache(ZGetMyUID());
				if (pCache && pCache->CheckFlag(MTD_PlayerFlags_AdminHide))
				{
					sprintf( szMsg, "^%d%s^9 ½º½º·Î ÆÐ¹è",
									(pAttacker->GetTeamID() == MMT_BLUE) ? 3 : 1,
									pAttacker->GetProperty()->GetName());
					ZGetGameInterface()->GetCombatInterface()->m_AdminMsg.OutputChatMsg( szMsg);
				}
			}
		}

	}

Replace <br>

		else
		{
			//sprintf(szMsg, "%s´ÔÀÌ ½º½º·Î ÆÐ¹èÇÏ¿´½À´Ï´Ù.", szAttacker);
			ZTransMsg( szMsg, MSG_GAME_WHO_LOSE_SELF, 1, szAttacker );
			ZChatOutput(MCOLOR(0xFF707070), szMsg);

			// Admin Grade
			if (ZGetMyInfo()->IsAdminGrade()) {
				MMatchObjCache* pCache = ZGetGameClient()->FindObjCache(ZGetMyUID());
				if (pCache && pCache->CheckFlag(MTD_PlayerFlags_AdminHide))
				{
					//Added pAttacker check (stupid MAIET)
					if (pAttacker)
					{
						//Changed string
						sprintf( szMsg, "^%d%s^9 has committed suicide.",//sprintf( szMsg, "^%d%s^9 ½º½º·Î ÆÐ¹è",
										(pAttacker->GetTeamID() == MMT_BLUE) ? 3 : 1,
										pAttacker->GetProperty()->GetName());
						ZGetGameInterface()->GetCombatInterface()->m_AdminMsg.OutputChatMsg( szMsg);
					}
					else
					{
						sprintf( szMsg, "^9%s^9 has committed suicide.", szAttacker);
						ZGetGameInterface()->GetCombatInterface()->m_AdminMsg.OutputChatMsg( szMsg);
					}
				}
			}
		}

	}

Find <br>

	bool ZGame::CanAttack(ZObject *pAttacker, ZObject *pTarget)

Replace <br>

	bool ZGame::CanAttack(ZObject *pAttacker, ZObject *pTarget)
	{
		//### ÀÌ ÇÔ¼ö¸¦ ¼öÁ¤ÇÏ¸é ¶È°°ÀÌ CanAttack_DebugRegister()¿¡µµ Àû¿ëÇØ ÁÖ¾î¾ß ÇÕ´Ï´Ù. ###
		if(!IsReplay())
			if(GetMatch()->GetRoundState() != MMATCH_ROUNDSTATE_PLAY) return false;
		if(pAttacker==NULL) return true;

		//Silly Gunz crash fix
		if (pTarget == NULL)
			return false;

		if ( GetMatch()->IsTeamPlay() ) {
			//Element crash bug fix. Not a really nice fix
			__try
			{
				if (pAttacker->GetTeamID() == pTarget->GetTeamID()) {
					if (!GetMatch()->GetTeamKillEnabled()) 
						return false;
				}
			}
			__except(EXCEPTION_EXECUTE_HANDLER) // GetExceptionCode() == EXCEPTION_ACCESS_VIOLATION 
			{
				mlog("MERROR_1\n");
				return false;
			}
		}

Find <br>

	bool ZGame::CanAttack_DebugRegister(ZObject *pAttacker, ZObject *pTarget)
	{

Replace <br>

	bool ZGame::CanAttack_DebugRegister(ZObject *pAttacker, ZObject *pTarget)
	{
		if(!IsReplay())
			if(GetMatch()->GetRoundState() != MMATCH_ROUNDSTATE_PLAY) return false;
		if(pAttacker==NULL) return true;

		//Silly Gunz crash fix
		if (pTarget == NULL)
			return false;

		if ( GetMatch()->IsTeamPlay() ) {
			if (pAttacker->GetTeamID() == pTarget->GetTeamID()) {
				if (!GetMatch()->GetTeamKillEnabled()) 
					return false;
			}
		}

	#ifdef _QUEST
		if (ZGetGameTypeManager()->IsQuestDerived(ZGetGameClient()->GetMatchStageSetting()->GetGameType()))
		{
			if (pAttacker->GetTeamID() == pTarget->GetTeamID())
			{
				return false;
			}
		}

	#endif
		return true;
	}


Open(ZGameClient.cpp) <br>
Find <br>

	void ZGameClient::RequestOnLobbyCreated()

Replace <br>

	void ZGameClient::RequestOnLobbyCreated()
	{
		ZPostRequestStageList(GetPlayerUID(), GetChannelUID(), 0);
		ZPostRequestChannelPlayerList(GetPlayerUID(), GetChannelUID(), 0);

		//Auto-load friend list
		ZPostFriendList();
	}

Open(ZGameInput.cpp) <br>
Find <br>

	void ZGameInput::GameCheckSequenceKeyCommand()

Replace <br>

	void ZGameInput::GameCheckSequenceKeyCommand()
	{
		// Ã¶Áö³­ Å° ÀÔ·ÂÀº ÀÏ´Ü Á¦°ÅÇÑ´Ù.
		while(m_ActionKeyHistory.size()>0 && (ZGetGame()->GetTime()-(*m_ActionKeyHistory.begin()).fTime>MAX_KEY_SEQUENCE_TIME))
		{
			m_ActionKeyHistory.erase(m_ActionKeyHistory.begin());
		}

		//Iterator decrement fix
		if(m_ActionKeyHistory.size())
		{
			for(int ai=0;ai<(int)m_SequenceActions.size();ai++)
			{
				ZKEYSEQUENCEACTION action=m_SequenceActions.at(ai);

				list<ZACTIONKEYITEM>::reverse_iterator itr = m_ActionKeyHistory.rbegin();

				bool bAction = true;

				for( int i = action.nKeyCount - 1; i >= 0; --i )
				{
					// fix
					if( itr == m_ActionKeyHistory.rend() )
					{
						bAction = false;
						break;
					}

					ZACTIONKEYITEM itm=*itr;

					if(i==0)
					{
						if(ZGetGame()->GetTime()-itm.fTime>action.fTotalTime)
						{
							bAction=false;
							break;
						}
					}
					if(itm.nActionKey!=action.pKeys[i].nActionKey || itm.bPressed!=action.pKeys[i].bPressed)
					{
						bAction=false;
						break;
					}
					if(i!=0 && itr==m_ActionKeyHistory.rend()) 
					{
						bAction=false;
						break;
					}

					++itr;
				}

				if(bAction)
				{
					if( m_ActionKeyHistory.size() )
						m_ActionKeyHistory.clear();

					if(ai>=0 && ai<=3)
						ZGetGame()->m_pMyCharacter->OnTumble(ai);
				}

				/*
				list<ZACTIONKEYITEM>::iterator itr=m_ActionKeyHistory.end();
				itr--;
				bool bAction=true;
				for(int i=action.nKeyCount-1;i>=0;i--)
				{
					ZACTIONKEYITEM itm=*itr;
					if(i==0)
					{
						if(ZGetGame()->GetTime()-itm.fTime>action.fTotalTime)
						{
							bAction=false;
							break;
						}
					}
					if(itm.nActionKey!=action.pKeys[i].nActionKey || itm.bPressed!=action.pKeys[i].bPressed)
					{
						bAction=false;
						break;
					}
					if(i!=0 && itr==m_ActionKeyHistory.begin()) 
					{
						bAction=false;
						break;
					}
					itr--;
				}

				if(bAction)
				{
					while(m_ActionKeyHistory.size())
					{
						m_ActionKeyHistory.erase(m_ActionKeyHistory.begin());
					}

					if(ai>=0 && ai<=3)		// ´ýºí¸µ
						ZGetGame()->m_pMyCharacter->OnTumble(ai);
				}
				*/
			}
		}
	}


Open(ZShopEquipItem.cpp) <br>
Find <br>

	static MMatchItemDesc nullItem;
	MMatchItemDesc* pEquipedItemDesc = &nullItem;
	MMatchCharItemParts parts = ZGetGameInterface()->GetShopEquipInterface()->RecommendEquipParts( m_pItemDesc->m_nSlot);
	ZMyItemNode* pMyItemNode = ZGetMyInfo()->GetItemList()->GetEquipedItem( parts);
	if ( pMyItemNode)
		pEquipedItemDesc = MGetMatchItemDescMgr()->GetItemDesc( pMyItemNode->GetItemID());

Add under <br>

	//Shop equip bugfix
	if (!pEquipedItemDesc)
		return;


Open(ZCharacter.h) <br>
Find <br>

	struct ZUserAndClanName
	{
		char m_szUserName[MATCHOBJECT_NAME_LENGTH];
		char m_szUserAndClanName[MATCHOBJECT_NAME_LENGTH];
	};

Replace <br>

	struct ZUserAndClanName
	{
		char m_szUserName[MATCHOBJECT_NAME_LENGTH];
		//Char name + Clan name fix
		char m_szUserAndClanName[MATCHOBJECT_NAME_LENGTH + CLAN_NAME_LENGTH];
	};

Open(ZGameClient.cpp) <br>
Find <br>

	case ZAAT_MSGBOX:
		{
			if (ZApplication::GetGameInterface()->GetState() != GUNZ_GAME)
			{
				ZApplication::GetGameInterface()->ShowMessage(szMsg);
			}
			else
			{
				ZChatOutput(szMsg);
			}
		}
		break;

Replace <br>

	case ZAAT_MSGBOX:
		{
			if (ZApplication::GetGameInterface()->GetState() != GUNZ_GAME)
			{
				ZApplication::GetGameInterface()->ShowMessage(szMsg);
			}
			else
			{
				//Fixed Gunz bug - popup announces showing in-game as normal text, not Green.
				char szText[512];
				ZTransMsg( szText, MSG_ADMIN_ANNOUNCE, 1, szMsg );
				ZChatOutput(szText, ZChat::CMT_SYSTEM);

				//ZChatOutput(szMsg);
			}
		}
		break;

Open(ZGameClient.cpp) <br>
Find <br>

		ZPlayerInfo* pInfo = ZGetPlayerManager()->Find( (MUID)uidChar);
		if ( pInfo != NULL)
		{
			sprintf( kill, "%d %s", pInfo->GetKill(), ZMsg( MSG_CHARINFO_KILL));
			sprintf( death, "%d %s", pInfo->GetDeath(), ZMsg( MSG_CHARINFO_DEATH));
			sprintf( winning, "%.1f%%", pInfo->GetWinningRatio());
		}
		else
		{
			sprintf( kill, "? %s", ZMsg( MSG_CHARINFO_KILL));
			sprintf( death, "? %s", pInfo->GetDeath(), ZMsg( MSG_CHARINFO_DEATH));
			sprintf( winning, "0.0%%");
		}

Replace <br>

		ZPlayerInfo* pInfo = ZGetPlayerManager()->Find( (MUID)uidChar);
		if ( pInfo != NULL)
		{
			sprintf( kill, "%d %s", pInfo->GetKill(), ZMsg( MSG_CHARINFO_KILL));
			sprintf( death, "%d %s", pInfo->GetDeath(), ZMsg( MSG_CHARINFO_DEATH));
			sprintf( winning, "%.1f%%", pInfo->GetWinningRatio());
		}
		else
		{
			sprintf( kill, "? %s", ZMsg( MSG_CHARINFO_KILL));
			//Removed pInfo->GetDeath(), from underlying code as it caused crashes
			sprintf( death, "? %s", ZMsg( MSG_CHARINFO_DEATH));
			sprintf( winning, "0.0%%");
		}

Open(ZItemSlotView.cpp) <br>
Find <br>

		unsigned long int nItemID = ZGetMyInfo()->GetItemList()->GetEquipedItemID(m_nParts);
		MMatchItemDesc* pItemDesc = NULL;
		if (nItemID != 0)
		{
			pItemDesc = MGetMatchItemDescMgr()->GetItemDesc(nItemID);

Add under <br>

		//Item slot view bugfix crash
		if (!pItemDesc) return false;



























