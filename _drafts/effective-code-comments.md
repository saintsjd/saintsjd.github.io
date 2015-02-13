Say why not what

// WHAT if the admin tries to save a map, deny that request.
if ( $this->secure_session['a'] == true ) { $this->forbidden(); return; }

// WHY prevent admin from creating maps because it taints the input data pool
