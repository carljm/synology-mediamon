MediaMon
========

File monitor and auto-indexer for Synology DiskStation NAS.

I'm using this on a DS213j, but I expect it may work on other models as well.

Updated for DSM 6.0+. May still work on earlier DSM versions, but I can't vouch
for that.


Usage
-----

1. Install Python3 from the DiskStation package manager.


2. Copy ``mediamon.py`` and ``S99mediamon.sh`` to your admin user's homedir
   (i.e. ``/volume1/homes/admin/``): ``scp *.{py,sh} admin@192.168.1.20:~``.

3. SSH into your DiskStation as admin (e.g. ``ssh admin@192.168.1.20`` -- use
   the right IP address for your DiskStation) and ``sudo su -`` to become the
   root user. This will require re-entering your admin user's password.

4. Install pyinotify::

    python3 -m ensurepip
    python3 -m pip install pyinotify

   Test that pyinotify works:
    python3 -m pyinotify -v /tmp

5. Copy ``S99mediamon.sh`` to the DiskStation's ``/usr/local/etc/rc.d/``
   directory, with ``0755`` permissions: ``cp
   /volume1/homes/admin/S99mediamon.sh /usr/local/etc/rc.d/ && chmod 755
   /usr/local/etc/rc.d/S99mediamon.sh``.
   
6. Restart your Synology (if you want to verify that the mediamon service will
   start up automatically in the future), or start it up yourself:
   ``/usr/local/etc/rc.d/S99mediamon.sh start``.

7. Add some media files to ``/volume1/photo``, ``/volume1/music``, or
   ``/volume1/video``, and check the log at ``/var/log/mediamon.log`` to verify
   that it's working. You should see a ``synoindex -a`` entry for each added
   file.


Caveats
-------

If you have a lot of files/directories in some watched volumes, you may see "No
space on device" errors from pyinotify. This doesn't actually have to do with
space on the drive, it means you're hitting the watched-files limit. You can
increase this limit by running (as root): ``echo
fs.inotify.max_user_watches=100000 | sudo tee -a /etc/sysctl.conf; sudo sysctl
-p``.

Suggestions, improvements, bug reports or pull requests welcome!


Credits
-------

Based on `a blog post`_.

.. _a blog post: https://codesourcery.wordpress.com/2012/11/29/more-on-the-synology-nas-automatically-indexing-new-files/
