# chrome-remote-desktop installation ubuntu 20.04
	sudo dpkg -i google-chrome-stable_current_amd64.deb

	sudo groupadd chrome-remote-desktop

	sudo usermod -a -G chrome-remote-desktop $USER

	cd ~

	touch ./.chrome-remote-desktop-session

	sudo gedit /opt/google/chrome-remote-desktop/chrome-remote-desktop

make the following changes to the file:

		  DEFAULT_SIZES = "1920x1080"

		  FIRST_X_DISPLAY_NUMBER = 0

		  #while os.path.exists(X_LOCK_FILE_TEMPLATE % display):
		  # display += 1


		  def launch_session(self, server_args, backoff_time):
		    """Launches process required for session and records the backoff time
		    for inhibitors so that process restarts are not attempted again until
		    that time has passed."""
		    logging.info("Setting up and launching session")
		    self._init_child_env()
		    self.setup_audio()
		    self._setup_gnubby()
		    '''
		    self._launch_server(server_args)
		    if not self._launch_pre_session():
		      # If there was no pre-session script, launch the session immediately.
		      self.launch_desktop_session()
		    '''
		    display = self.get_unused_display_number()
		    self.child_env["DISPLAY"] = ":%d" % display
		    self.child_env["CHROME_REMOTE_DESKTOP_SESSION"] = "1"
		    self.server_inhibitor.record_started(MINIMUM_PROCESS_LIFETIME,
						      backoff_time)
		    self.session_inhibitor.record_started(MINIMUM_PROCESS_LIFETIME,
						     backoff_time)
		                             
	
/opt/google/chrome-remote-desktop/chrome-remote-desktop --start

For some computers, the service might not able to run, you could try changing the following

		FIRST_X_DISPLAY_NUMBER = 1

Let the user auto login:

	sudo gnome-control-center
	#or
	 	sudo nano /etc/gdm3/custom.conf
	  	#uncomment
	   	AutomaticLoginEnable = true
		AutomaticLogin = [username]
	 	#comment
	  	#WaylandEnable=false
 


After setting up auto login, add the following file to avoid authenticate popup

Ref: http://c-nergy.be/blog/?p=12043

	sudo nano /etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla

		[Allow Colord all Users]
		Identity=unix-user:*
		Action=org.freedesktop.color-manager.create-device;org.freedesktop.color-manager.create-profile;org.freedesktop.color-manager.delete-device;org.freedesktop.color-manager.delete-profile;org.freedesktop.color-manager.modify-device;org.freedesktop.color-manager.modify-profile;org.freedesktop.packagekit.system-sources-refresh
		ResultAny=no
		ResultInactive=no
		ResultActive=yes
	
	sudo rm /var/crash/*

  	sudo nano /etc/polkit-1/localauthority/50-local.d/46-allow-update-repo.pkla
   		[Allow Package Management all Users]
		Identity=unix-user:*
		Action=org.freedesktop.packagekit.system-sources-refresh
		ResultAny=yes
		ResultInactive=yes
		ResultActive=yes

Reboot your computer
